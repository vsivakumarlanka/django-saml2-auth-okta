ChangeLog of Django SAML2 Auth AI
=================================

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
