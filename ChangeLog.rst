ChangeLog of Django SAML2 Auth AI
=================================

2.1.1
-----

Released at 2019-03-29 08:20 +0200.

- Fix assumption that "admin:index" URL always exists

  - Now it is possible to run the app without Django Admin as long as
    the DEFAULT_NEXT_URL setting is configured

2.1.0
-----

Released at 2019-01-17 09:15 +0200.

- Add new hooks TRIGGER.FIND_USER and TRIGGER.NEW_USER.

- Add support for Django 2.0

2.0.0
-----

Released at 2018-09-12 11:10 +0300.

- Pass also the user as parameter to callback calls

1.2.0
-----

Released at 2018-09-05 13:25 +0300.

- Support custom User model

  - Utilize Django's get_user_model function to get the active User
    model which might be customized with the AUTH_USER_MODEL setting

- Support populating any User field from the SAML reply

  - Implement copying data from the SAML reply to any field of the User
    object by specifying the fields in the ATTRIBUTES_MAP setting

1.1.0
-----

Released at 2018-08-24 15:00 +0300.

- Allow passing through pysaml2 client settings

1.0.1
-----

Released at 2018-08-24 14:50 +0300.

- Fix formatting error in README

1.0.0
-----

Released at 2018-08-24 14:40 +0300.

- Initial release of the Django SAML2 Auth AI fork
