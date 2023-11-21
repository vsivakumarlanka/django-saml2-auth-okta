Django SAML2 Authentication OKTA
==============================

This project aims to provide a dead simple way to integrate SAML2
Authentication into your Django powered app. Try it now, and get rid of the
complicated configuration of SAML.

Any SAML2 based SSO (Single-Sign-On) identity provider with dynamic metadata
configuration is supported by this Django plugin, for example Okta.

This project is a fork of django-saml2-auth_ by `Fang Li`_.
This project is a fork of django-saml2-auth_ai by `Andersino`_.

.. _django-saml2-auth: https://github.com/fangli/django-saml2-auth
.. _`Fang Li`: https://github.com/fangli

.. _django-saml2-auth-ai: https://github.com/andersinno/django-saml2-auth-ai
.. _`andersinno`: https://github.com/andersinno

|PyPI|

.. |PyPI| image::
   https://img.shields.io/pypi/v/django-saml2-auth-ai.svg
   :target: https://pypi.org/project/django-saml2-auth-ai/


Dependencies
------------

This plugin is compatible with Django 4.0.8
The `pysaml2` Python module is required.


Install
-------

You can install this plugin via `pip`:

.. code-block:: bash

    # pip install django-saml2-auth-okta

or from source:

.. code-block:: bash

    # git clone https://github.com/vsivakumarlanka/django-saml2-auth-okta
    # cd django-saml2-auth-okta
    # python setup.py install

xmlsec is also required by pysaml2:

.. code-block:: bash

    # yum install xmlsec1
    // or
    # apt install xmlsec1


What does this plugin do?
-------------------------

This plugin takes over Django's login page and redirect the user to a SAML2
SSO authentication service. Once the user is logged in and redirected back,
the plugin will check if the user is already in the system. If not, the user
will be created using Django's default UserModel, otherwise the user will be
redirected to their last visited page.



How to use?
-----------

#. Import the views module in your root urls.py

    .. code-block:: python

        import django_saml2_auth.views

#. Override the default login page in the root urls.py file, by adding these
   lines **BEFORE** any `urlpatterns`:

    .. code-block:: python

        # These are the SAML2 related URLs. You can change "^saml2_auth/" regex to
        # any path you want, like "^sso_auth/", "^sso_login/", etc. (required)
        
        path('saml2_auth/', include('django_saml2_auth.urls')),   

        # The following line will replace the default user login with SAML2 (optional)
        # If you want to specific the after-login-redirect-URL, use parameter "?next=/the/path/you/want"
        # with this view.
        path('accounts/login/', django_saml2_auth.views.signin), 

        # The following line will replace the admin login with SAML2 (optional)
        # If you want to specific the after-login-redirect-URL, use parameter "?next=/the/path/you/want"
        # with this view.
        path('admin/login/', django_saml2_auth.views.signin), 


        ### custom urls to use both sso and manual login (optional)
        ## If you want to use both manual login and sso login then you can change the urls to be following(optional)
        path('saml2_auth/login/', django_saml2_auth.views.signin,name='ssologin'),
        path(r'accounts/login/', auth_views.LoginView.as_view(template_name='your_folder/login.html'), {'template_name': 'your_folder/login.html'}, name='login'),
        path(r'accounts/logout/', auth_views.LogoutView.as_view(template_name='your_folder/logout.html'), {'template_name': 'your_folder/logout.html'}, name='logout'),



#. Add 'django_saml2_auth' to INSTALLED_APPS

    .. code-block:: python

        INSTALLED_APPS = [
            '...',
            'django_saml2_auth',
        ]

#. In settings.py, add the SAML2 related configuration.

    Please note, the only required setting is **METADATA_AUTO_CONF_URL**.
    The following block shows all required and optional configuration settings
    and their default values.

    .. code-block:: python

        SAML2_AUTH = {
            # Required setting
            'SAML_CLIENT_SETTINGS': { # Pysaml2 Saml client settings (https://pysaml2.readthedocs.io/en/latest/howto/config.html)
                'entityid': 'https://mysite.com/saml2_auth/acs/', # The optional entity ID string to be passed in the 'Issuer' element of authn request, if required by the IDP.
                'metadata': {
                    'remote': [
                        {
                            "url": 'https://mysite.com/metadata.xml', # The auto(dynamic) metadata configuration URL of SAML2
                        },
                    ],
                },
            },

            # Optional settings below
            'DEFAULT_NEXT_URL': '/',  # Custom target redirect URL after the user get logged in. Default to /admin if not set. This setting will be overwritten if you have parameter ?next= specificed in the login URL.
            'NEW_USER_PROFILE': {
                'USER_GROUPS': [],  # The default group name when a new user logs in
                'ACTIVE_STATUS': True,  # The default active status for new users
                'STAFF_STATUS': True,  # The staff status for new users
                'SUPERUSER_STATUS': False,  # The superuser status for new users
            },
            'ATTRIBUTES_MAP': {  # Change Email/UserName/FirstName/LastName to corresponding SAML2 userprofile attributes.
                'email': 'Email',
                'username': 'UserName',
                'first_name': 'FirstName',
                'last_name': 'LastName',
            },
            'TRIGGER': {
                'FIND_USER': 'path.to.your.find.user.hook.method',
                'NEW_USER': 'path.to.your.new.user.hook.method',
                'CREATE_USER': 'path.to.your.create.user.hook.method',
                'BEFORE_LOGIN': 'path.to.your.login.hook.method',
            },
            'ASSERTION_URL': 'https://mysite.com', # Custom URL to validate incoming SAML requests against
        }

#. In your SAML2 SSO identity provider, set the Single-sign-on URL and Audience
   URI(SP Entity ID) to http://your-domain/saml2_auth/acs/


Explanation
~~~~~~~~~~~

**NEW_USER_PROFILE** Default settings for newly created users

**ATTRIBUTES_MAP** Mapping of Django user attributes to SAML2 user attributes

**TRIGGER** Hooks to trigger additional actions during user login and creation
flows. These TRIGGER hooks are strings containing a `dotted module name <https://docs.python.org/3/tutorial/modules.html#packages>`_
which point to a method to be called. The referenced method should accept a
single argument which is a dictionary of attributes and values sent by the
identity provider, representing the user's identity.

**TRIGGER.FIND_USER** A function to be called when trying to find user.
The function is called with one positional argument: a dictionary of the
user data received from SAML.

**TRIGGER.CREATE_USER** A function to be called upon new user creation.
It will be called before the new user is logged in and after the user's
record is created.  The function is called with two positional
arguments: User model instance and a dictionary of the user data
received from SAML.

**TRIGGER.NEW_USER** A function to be called upon new user creation.
It will be called before the user's record is saved. The function is
called with two positional arguments: User model instance and a dictionary
of the user data received from SAML.

**TRIGGER.BEFORE_LOGIN** A function to be called when an existing user
logs in.  It will be called before the user is logged in and after the
user attributes are returned by the SAML2 identity provider. The
function is called with two positional arguments: User model instance
and a dictionary of the user data received from SAML.

**ASSERTION_URL** A URL to validate incoming SAML responses against. By default,
django-saml2-auth will validate the SAML response's Service Provider address
against the actual HTTP request's host and scheme. If this value is set, it
will validate against ASSERTION_URL instead - perfect for when django running
behind a reverse proxy.

Customize
---------

The default permission `denied` page and user `welcome` page can be
overridden.

To override these pages put a template named 'django_saml2_auth/welcome.html'
or 'django_saml2_auth/denied.html' in your project's template folder.

If a 'django_saml2_auth/welcome.html' template exists, that page will be shown
to the user upon login instead of the user being redirected to the previous
visited page. This welcome page can contain some first-visit notes and welcome
words. The `Django user object <https://docs.djangoproject.com/en/1.9/ref/contrib/auth/#django.contrib.auth.models.User>`_
is available within the template as the `user` template variable.

To enable a logout page, add the following lines to urls.py, before any
`urlpatterns`:

.. code-block:: python

    # The following line will replace the default user logout with the signout page (optional)
    url(r'^accounts/logout/$', django_saml2_auth.views.signout),

    # The following line will replace the default admin user logout with the signout page (optional)
    url(r'^admin/logout/$', django_saml2_auth.views.signout),

To override the built in signout page put a template named
'django_saml2_auth/signout.html' in your project's template folder.

If your SAML2 identity provider uses user attribute names other than the
defaults listed in the `settings.py` `ATTRIBUTES_MAP`, update them in
`settings.py`.


For Okta Users
--------------

I created this plugin originally for Okta.

The METADATA_AUTO_CONF_URL needed in `settings.py` can be found in the Okta
web UI by navigating to the SAML2 app's `Sign On` tab, in the Settings box.
You should see :

`Identity Provider metadata is available if this application supports dynamic configuration.`

The `Identity Provider metadata` link is the METADATA_AUTO_CONF_URL.


How to Contribute
-----------------

#. Check for open issues or open a fresh issue to start a discussion around a feature idea or a bug.
#. Fork `the repository`_ on GitHub to start making your changes to the **master** branch (or branch off of it).
#. Write a test which shows that the bug was fixed or that the feature works as expected.
#. Send a pull request and bug the maintainer until it gets merged and published. :) Make sure to add yourself to AUTHORS_.

.. _`the repository`: http://github.com/andersinno/django-saml2-auth-ai
.. _AUTHORS: https://github.com/andersinno/django-saml2-auth-ai/blob/master/AUTHORS.rst

License
-------

Copyright 2016-2018 Fang Li

Copyright 2018 Anders Innovations

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
