==============================
OEP-0031: Internationalization
==============================

.. list-table::

   * - OEP
     - :doc:`OEP-0031 <oep-0031-arch-i18n>`
   * - Title
     - Internationalization
   * - Last Modified
     - 2019-01-07
   * - Authors
     -
   * - Arbiter
     - Simon Chen <schen@edx.org>
   * - Status
     - Draft
   * - Type
     - Architecture
   * - Created
     - 2019-01-07
   * - Review Period
     -

.. contents::
   :local:
   :depth: 2

Motivation
==========

Internationalization (i18n) is a vital part of unlocking content for users around the globe and effectively supporting our business partners. In order to help us reuse localized components from app to app, the different parts of the edX codebase should align on a consistent handling of i18n. This document covers our approach to internationalizing new front-end applications and components written in React.

In this OEP we use the terms locale and language more or less interchangeably. However, note that this document addresses primarily making code multilingual, i.e., the issue of presenting the same content in different languages. Considering ways to make it multi-regional, to allow for presenting different content for different countries, is out of scope for this OEP.

Technology Choice
=================

We will use `react-intl <https://github.com/yahoo/react-intl>`_ to internationalize React components, in agreement with `Paragon ADR-2 <https://github.com/edx/paragon/blob/f5b9f876800e5154ef01d04f682b8cda21b3e439/docs/decisions/0002-react-i18n.rst>`_. react-intl is in line with current industry standards in i18n and satisfies all currently identified i18n needs, including multiple plurals.

Alternatives Considered
-----------------------

An alternative choice we considered was `i18next <https://react.i18next.com/>`_. It comes with plug-and-play functionality to determine a user's locale, babel scripts for various translation management schemes, etc.  However, it is fairly large, and doesn't seem to offer compelling benefits that would outweigh the fact that studio-frontend and Paragon already use react-intl.

URLs
====

- describe linksharing problem
- SEO
- add a link to the google explanation site
- decision: different for anonymous, vs. logged in
- Marco: can enterprise make the path prefix optional?

For pages intended to be viewable by the public (including search engines), we will use path prefixing to identify the locale of the page being shown. This means that the URL will include the locale as the first part of the path after the domain. (Example: ``account.edx.org/es/``) This language code should be in the form of a `BCP 47 language tag <http://tools.ietf.org/html/rfc5646>`_.

**[TODO more about codes vs. countries here]**


 If the user enters the site at a URL that doesn't include a language string, we will determine a locale and then forward them to the corresponding URL. For consistency, this applies even if their language is English: ``account.edx.org/`` should be forwarded to ``account.edx.org/en/`` .

Alternatives Considered
-----------------------

We chose to avoid putting the locale in the domain name itself, to leave that available for other uses. We considered but rejected putting it in as a URL parameter; that can be harder to parse out, doesn't give an SEO benefit, and doesn't give us automatic separation of analytics by locale. Using the first component of the path ensures that if users send a link or bookmark a page, their recipients will see the exact content that they intended to share.

Locale Selection
================

Different parts of the site might have different requirements in terms of how a locale is selected, given information we have about the user.

When choosing what locale to use for a page, the locale in the URL always takes precedence. TODO NOT ANY ORE If there is none, check the user's cookies for a locale; if there isn't one, fetch it from the user's account settings and store it in their cookies for reuse.  TODO LOCAL STORAGE If there is no locale (or no locale we support) in the account settings, we will use the first locale we can support in the ``Accept-Language`` header that was sent with the request. Failing everything else, we will fall back to English.

TODO add that we update the url

Alternatives Considered
-----------------------

We considered but rejected a fallback step where we would choose a language based on the geographic location of the user's IP address. This is generally not reliable: many countries have speakers of multiple languages, users might be traveling, and users might be living or studying in a country but still be more comfortable in a different language.

Accessibility Requirements
==========================

Translate all non-display text that is present in a tag for accessibility reasons (e.g., ``aria-label``), using the same locale as the rest of the page.

Message Ids
===========

Message ids, or message keys, uniquely identify a string to be internationalized. They should consist of one or more words to namespace the message, plus one or more words to identify the message. All words will be lowercase and separated by periods. Here are examples of message ids used in a header and in a login modal, respectively:

  | ``header.search``
  | ``login.no.such.password``
  | ``login.privacy.policy``

The part of the id used for namespacing is purely to reduce collisions and help human readability; it's not intended to be programmatically parsable.

- add a stronger rec. here! translations shouldn't conflict

Alternatives Considered
-----------------------

Previous i18n implementations have sometimes used the English text as the message id. While this saves developers a step in their work, it has the drawback that a change in the English copy invalidates all existing translations, even if they would not otherwise have needed to change. (For example, capitalizing a noun in English has no impact on the German translation, because nouns are capitalized in German anyway, or an English change might be correcting a typo that was correct in the translations.) Do not use English copy as the message id.

Dates And Numbers
=================

All dates and numbers should be localized.

TODO consider phone numbers

Right-To-Left Locales
=====================

**[TODO]**

Translation Versioning
======================

**[TODO]**

Missing Translations
====================

Missing translations can be a disorienting experience for a user. We strongly suggest a careful evaluation of translation coverage, or a clear indicator of beta status, before making a new language available to the public.

Reference Implementation
========================

**[TODO This will happen in the Account Profile repo.]**

TODO components????

Backward Compatibility
======================

**[TODO will e.g. Prospectus be able to use the new localized components? studio-frontend uses react-intl but in a different way than described here, and relies on edx-platform to provide the translations (i.e., it does not generate localized bundles).]**

Future Work
===========

There is a special accessibility use case where a message id doesn't have a translation in the user's preferred locale, so we fall back to a default locale. In this case, the string would ideally be wrapped in a ``<span locale="....">`` tag to maintain the correct locale information for accessibility tools. In order to make sure that this string wrapping always happens, studio-frontend uses ``WrappedMessage``, a simple wrapper around the standard react-intl ``FormattedMessage`` component.

We should correctly localize currency, which is an issue of country rather than language.

References
==========

Choosing a language tag: https://www.w3.org/International/articles/language-tags/

Plurals in different languages: https://developer.mozilla.org/en-US/docs/Mozilla/Localization/Localization_and_Plurals

Multilingual vs. multiregion:

Change History
==============
