###################################################
OEP-0031: Internationalization For React Front Ends
###################################################

.. list-table::

   * - OEP
     - :doc:`OEP-0031 <oep-0031-arch-i18n.rst>`
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
   :depth: 3

##########
Motivation
##########

Internationalization (i18n) is a vital part of unlocking content for users around the globe and effectively supporting our business partners. In order to help us reuse localized components from app to app, the different parts of the edX codebase should align on a consistent handling of i18n. This document covers our approach to internationalizing new front-end applications and components written in React. This document doesn't necessarily cover internationalization for other technologies, such as the Django templates in edx-platform.

###########
Terminology
###########

We will use the term *localized* to refer to a view of a web page that is customized with a user-appropriate language, time zone, number format, and currency. We will use the term *internationalized* to refer to a web page that is designed to have its text, dates, prices, etc. easily swapped out in order to create different localized versions.

In this OEP we use the terms *locale* and *language* more or less interchangeably. However, note that this document mainly addresses making code multilingual, i.e., the issue of presenting the same content in different languages. Considering ways to make it multi-regional, to allow for presenting different content for different countries, is out of scope for this OEP.

*Transifex* is the third-party system that lets us upload messages for translation and download the translations to incorporate them into our software. Except where Transifex-specific concerns are noted, this document will still apply if we change translation systems.

#############
Specification
#############

*************************************
Industry Standards And Best Practices
*************************************

HTML Considerations
===================

Set the language of your document with the ``lang`` attribute on your ``html`` tag. Use the UTF-8 encoding, and declare it with a ``meta`` tag in the head of your HTML document. Prefer using characters (``á``) rather than character entities (``&aacute;``) or escapes (``&#225;``).

Not Everything Is English
=========================

Use longer strings with placeholder variables, rather than trying to create messages by concatenating two or more short messages together. Even if it looks like it's working in English, it won't necessarily be grammatically correct in other languages. This includes uses such as a pagination display: you cannot translate only the word ``of`` and then glue together ``{number} + {translation of "of"} + {number}``. Instead, your string for translation should be ``{page_number} of {total_pages}``, so your translators can move the placeholders into whatever position is required for their language.

When creating forms, keep in mind that there are many different ways of writing an address besides "street number and street name" as one field, and city and state as two others. Also, not every culture puts given name and family name in that order. If possible, allow free form, unstructured addresses, name fields, etc.

Accessibility
=============

Translate all non-display text that is present in a tag for accessibility reasons (e.g., ``aria-label``), using the same locale as the rest of the page.

Dates And Numbers
=================

All dates and numbers should be localized.

Currency
========

When displaying a price in a foreign (to the user) currency, localize the number according to the user's locale. That is, for a user in France viewing a price in U.S. dollars, show it as ``USD 19,99``, using the ``fr`` convention for the decimal point.

Right-To-Left Locales
=====================

For languages that are written right-to-left (RTL), such as Arabic and Hebrew, you will need to add a ``dir`` attribute to your ``html`` tag, and your CSS must swap graphics and margins. The frontend-i18n library will make this straightforward; see the `i18n HOWTO <https://github.com/edx/frontend-i18n/blob/master/docs/how_tos/i18n.rst>`_ for more information.

*****************
Locale Resolution
*****************

For pages meant to be seen by logged-in users, we will check the user's cookies for a locale; if there isn't one, we will fetch it from the user's account settings and store it in their cookies for reuse. If there is no locale (or no locale we support) in the account settings, we will use the locale that the user's browser is set to. Failing everything else, we will fall back to English.

Alternatives Considered
=======================

We considered but rejected a fallback step where we would choose a language based on the geographic location of the user's IP address. This is generally not reliable: many countries have speakers of multiple languages, users might be traveling, and users might be living or studying in a country but still be more comfortable in a different language.

****
URLs
****

For pages intended to be viewable by the public (including search engines), we will use path prefixing to identify the locale of the page being shown. This means that the URL will include the locale as the first part of the path after the domain. (Example: ``account.edx.org/es/``) This language code should be in the form of a `BCP 47 language tag <http://tools.ietf.org/html/rfc5646>`_.

 If the user enters the site at a URL that doesn't include a language string, we will determine a locale and then forward them to the corresponding URL. For consistency, this applies even if their language is English: ``account.edx.org/`` should be forwarded to ``account.edx.org/en/`` .

Alternatives Considered
=======================

We chose to avoid putting the locale in the domain name itself, to leave that available for other uses. We considered but rejected putting it in as a URL parameter; that can be harder to parse out, doesn't give an SEO benefit, and doesn't give us automatic separation of analytics by locale. Using the first component of the path ensures that if users send a link or bookmark a page, their recipients will see the exact content that they intended to share.

***********************
Translations Management
***********************

Message Ids
===========

Message ids, or message keys, uniquely identify a string to be internationalized. They should consist of one or more words to namespace the message, plus one or more words to identify the message. All words will be lowercase and separated by periods. Here are examples of message ids used in a header and in a login modal, respectively:

  | ``header.search``
  | ``login.no.such.password``
  | ``login.privacy.policy``

The part of the id used for namespacing is purely to reduce collisions and help human readability; it's not intended to be programmatically parsable. That said, a helpful convention is to make it the name of your application or repo. Note that if you end up with a duplicate message id in the same translation project in Transifex, and they have different content, one of them will "win" when you download your translated strings, leading to an out-of-place string in your application.

Alternatives Considered
-----------------------

Previous i18n implementations have sometimes used the English text as the message id. While this saves developers a step in their work, it has the drawback that a change in the English copy invalidates all existing translations, even if they would not otherwise have needed to change. (For example, capitalizing a noun in English has no impact on the German translation, because nouns are capitalized in German anyway. Or an English change might be correcting a typo that was correct in the translations.) Do not use English copy as the message id.

Cross-Site Scripting Vulnerability
==================================

Because translated strings often contain placeholders that will be filled in at render time, possibly using user input, localization is a potential opening for cross-site scripting (XSS) attacks. The react-intl functions exposed by frontend-i18n are XSS-safe.

Mapping Server Codes To Messages
================================

When a server response can contain one of a set of enumerated values, it can be tempting to assemble your message id on the fly by concatenating a common prefix and the server-provided value:

``const id = 'myapp.errors.${errorCode}';``

However, an unexpected value from the server can cause this technique to generate a message id that doesn't exist. If you choose to do this, it's best to whitelist your server-provided value against a list of expected responses.

Missing Translations
====================

Missing translations can be a disorienting experience for a user. We strongly suggest a careful evaluation of translation coverage, or a clear indicator of beta status, before making a new language available to the public.

****************
Server-Side i18n
****************

Have your server send symbolic values or error codes, rather than translated display text, to convey information, and then localize on the front end. This keeps all of your localization together, simplifies testing your server code, and makes your server response reusable by other front ends, which may wish to display the same information but with different text.

#################
Technology Choice
#################

**********
react-intl
**********

We will use `react-intl <https://github.com/yahoo/react-intl>`_ to internationalize React components, in agreement with `Paragon ADR-2 <https://github.com/edx/paragon/blob/master/docs/decisions/0002-react-i18n.rst>`_. Our needs included the ability to add notes for translators and to get a translated message as a plain string (as opposed to only as a React component). react-intl is in line with current industry standards in i18n and meets edX needs. In order to add some browser shims, the Architecture team has published a thin wrapper around react-intl in the `frontend-i18n repo <https://github.com/edx/frontend-i18n>`_. For consistency, use this wrapper, instead of importing react-intl directly.

Alternatives Considered
=======================

An alternative choice we considered was `i18next <https://react.i18next.com/>`_. It comes with plug-and-play functionality to determine a user's locale, babel scripts for various translation management schemes, and a lot of other supporting code. It also exposes a much cleaner interface for getting a translated message as a plain string. However, we would have had to write a custom solution for extracting translator comments, and it outputs a format that Transifex can't work with.

************************
Reference Implementation
************************

The reference implementation of this OEP is the Order History app in `frontend-app-ecommerce <https://github.com/edx/frontend-app-ecommerce>`_, which uses the `frontend-i18n <https://github.com/edx/frontend-i18n>`_ library.

###########
Future Work
###########

There is a special accessibility use case where a message id doesn't have a translation in the user's preferred locale, so we fall back to a default locale. In this case, the string would ideally be wrapped in a ``<span locale="....">`` tag to maintain the correct locale information for accessibility tools. In order to make sure that this string wrapping always happens, studio-frontend uses ``WrappedMessage``, a simple wrapper around the standard react-intl ``FormattedMessage`` component.

We should correctly localize currency, which is an issue of country rather than language.

We should also settle on a consistent set of language and locale codes across edX.

##########
References
##########

#. Multilingual vs. multiregion, best practices for URLs: https://support.google.com/webmasters/answer/182192?hl=en

#. Best practices: https://www.w3.org/International/quicktips/

#. International considerations for inputting human names: https://www.kalzumeus.com/2010/06/17/falsehoods-programmers-believe-about-names/

#. Plurals in different languages: https://developer.mozilla.org/en-US/docs/Mozilla/Localization/Localization_and_Plurals

#. Determining a language tag for a user: https://www.w3.org/International/articles/language-tags/

#. XSS in react-intl: https://edx.readthedocs.io/projects/edx-developer-guide/en/latest/preventing_xss/preventing_xss_in_react.html#i18n-and-translations

#. edX React App i18n HOWTO: https://github.com/edx/frontend-i18n/blob/master/docs/how_tos/i18n.rst

##############
Change History
##############

**********
2019-01-23
**********

* Document created.
