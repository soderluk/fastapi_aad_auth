Using fastapi_aad_auth
**********************
Please see `Basic Usage <usage>`_ for information on how to configure and setup ``fastapi_aad_auth``.

Accessing User Tokens/View
~~~~~~~~~~~~~~~~~~~~~~~~~~

There are two routes that are automatically added to this, the ``/me`` and ``/me/getToken`` routes. The ``/me`` route provides a summary of the current user, and enables them to get a bearer token from Azure AD.
The ``/me/token`` endpoint provides that same token (for the logged in user) in a JSON object

.. warning::

    To get the token, this is primarily an interactive method, as it requires caching the token through the UI session based login approach, so it can fail intermittently depending on if the user has logged in recently.

This can be disabled by setting the ``config.routing.user_path`` to ``None`` or ``''``. #

Customising the User Model
~~~~~~~~~~~~~~~~~~~~~~~~~~

The authentication state user can be processed within the application methods - the ``Depends`` part of the api route returns an
:class:`~fastapi_aad_auth.oauth.state.AuthenticationState` object - ``auth_state`` in the ``testapp`` (see :ref:`testing`).

.. literalinclude:: ../../tests/testapp/server.py
    :language: python
    :linenos:
    :start-at: @router.get('/hello')
    :end-at: return

The associated user is then available at ``auth_state.user``

The :class:`~fastapi_aad_auth.oauth.aad.AADOAuthBackend` object takes a ``user_klass`` argument:

.. literalinclude:: ../../src/fastapi_aad_auth/oauth/aad.py
    :language: python
    :linenos:
    :start-at: class AADOAuthBackend
    :end-before: """Initialise

which defaults to the really basic :class:`~fastapi_aad_auth.oauth.state.User` class, but any object with the same 
interface should work, so you can add e.g. database calls etc. to validate/persist/check the user and any other
desired behaviours.

You can customise this when initialising the :class:`~fastapi_aad_auth.auth.AADAuth` object by setting
the :class:`~fastapi_aad_auth.config.Config` ``user_klass`` variable (this can also be done by the
associated environment variable)::

    from fastapi_aad_auth import AADAuth, Config

    config = Config()
    config.user_klass = MyUserClass

    auth = AADAuth(config)

