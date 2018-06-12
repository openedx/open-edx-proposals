===========================
OEP-0024: API Rate Limiting
===========================

+-----------------+--------------------------------------------------------+
| OEP             | :doc:`OEP-0024 </oeps/oep-0024-bp-rate-limiting>`      |
+-----------------+--------------------------------------------------------+
| Title           | API Rate Limiting                                      |
+-----------------+--------------------------------------------------------+
| Last Modified   | 2018-06-12                                             |
+-----------------+--------------------------------------------------------+
| Authors         | Christopher Lee <clee@edx.org>                         |
+-----------------+--------------------------------------------------------+
| Arbiter         | TBD                                                    |
+-----------------+--------------------------------------------------------+
| Status          | Under Review                                           |
+-----------------+--------------------------------------------------------+
| Type            | Best Practice                                          |
+-----------------+--------------------------------------------------------+
| Created         | 2018-06-12                                             |
+-----------------+--------------------------------------------------------+
| `Review Period` | 2018-07-12                                             |
+-----------------+--------------------------------------------------------+

Abstract
========

Propose best practices for rate limiting APIs.

Open questions:

* What about rate limiting is in scope for this OEP?
* What/how do we set the initial rate limit at?
** Should we set a global rate limit?
* What is the process for increasing a rate limit?
* What is the process for scaling down a rate limit?
* What about different types of users? (Service, super, normal, etc.)

Motivation
==========

Prevent API

- Prevent a single resource from causing outages for all resources
- Restrict resource usage for low priority APIs.

To be able to safely introduce or scale down rate limits without causing an outage or rollback of a release.


Specifications
==============

Setting Rate Limits for DRF
---------------------------
Setting up a scoped throttle rate class::

    class ExampleUserThrottle(UserRateThrottle):
        THROTTLE_RATES = {
            'user': '40/minute',
            'staff': '120/minute',
        }

        def allow_request(self, request, view):
            # Use a special scope for staff to allow for a separate throttle rate
            user = request.user
            if user.is_authenticated and (user.is_staff or user.is_superuser):
                self.scope = 'staff'
                self.rate = self.get_rate()
                self.num_requests, self.duration = self.parse_rate(self.rate)

            return super(ExampleUserThrottle, self).allow_request(request, view)

Testing scopes are set correctly::

    @ddt.data('staff', 'user')
    def test_example_throttle_is_set_correctly(self, user_scope):
        """ Make sure throttle rate is set correctly for different user scopes. """
        self.rate_limit_config.enabled = True
        self.rate_limit_config.save()

        throttle = ExampleUserThrottle()
        throttle.scope = user_scope
        try:
            throttle.parse_rate(throttle.get_rate())
        except ImproperlyConfigured:
            self.fail("No throttle rate set for {}".format(user_scope))

Using Waffle Flags for Variable Rate Limits
-------------------------------------------
::

    class ExampleUserThrottle(UserRateThrottle):
        THROTTLE_RATES = {
            'user': '40/minute',
            'staff': '120/minute',
        }

        def allow_request(self, request, view):
            if USE_RATE_LIMIT_400_FOR_STAFF_FOR_EXAMPLE_API.is_enabled():
                self.THROTTLE_RATES = {
                    'user': '40/minute',
                    'staff': '400/minute',
                }
            elif USE_RATE_LIMIT_100_FOR_STAFF_FOR_EXAMPLE_API.is_enabled():
                self.THROTTLE_RATES = {
                    'user': '40/minute',
                    'staff': '100/minute',
                }
            elif USE_RATE_LIMIT_40_FOR_EXAMPLE_API.is_enabled():
                self.THROTTLE_RATES = {
                    'user': '40/minute',
                    'staff': '40/minute',
                }

            # Use a special scope for staff to allow for a separate throttle rate
            user = request.user
            if user.is_authenticated and (user.is_staff or user.is_superuser):
                self.scope = 'staff'
                self.rate = self.get_rate()
                self.num_requests, self.duration = self.parse_rate(self.rate)

            return super(EnrollmentUserThrottle, self).allow_request(request, view)

