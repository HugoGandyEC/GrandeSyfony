.. index::
   single: Configuration reference; Framework

FrameworkBundle Configuration ("framework")
===========================================

This reference document is a work in progress. It should be accurate, but
all options are not yet fully covered.

The ``FrameworkBundle`` contains most of the "base" framework functionality
and can be configured under the ``framework`` key in your application configuration.
This includes settings related to sessions, translation, forms, validation,
routing and more.

Configuration
-------------

* `charset`_
* `secret`_
* `ide`_
* `test`_
* `trust_proxy_headers`_
* `trusted_proxies`_
* `form`_
    * enabled
* `csrf_protection`_
    * enabled
    * field_name
* `session`_
    * `name`_
    * `lifetime`_
* `templating`_
    * `assets_base_urls`_
    * `assets_version`_
    * `assets_version_format`_

charset
~~~~~~~

**type**: ``string`` **default**: ``UTF-8``

The character set that's used throughout the framework. It becomes the service
container parameter named ``kernel.charset``.

secret
~~~~~~

**type**: ``string`` **required**

This is a string that should be unique to your application. In practice,
it's used for generating the CSRF tokens, but it could be used in any other
context where having a unique string is useful. It becomes the service container
parameter named ``kernel.secret``.

ide
~~~

**type**: ``string`` **default**: ``null``

If you're using an IDE like TextMate or Mac Vim, then Symfony can turn all
of the file paths in an exception message into a link, which will open that
file in your IDE.

If you use TextMate or Mac Vim, you can simply use one of the following built-in
values:

* ``textmate``
* ``macvim``

You can also specify a custom file link string. If you do this, all percentage
signs (``%``) must be doubled to escape that character. For example, the
full TextMate string would look like this:

.. code-block:: yaml

    framework:
        ide:  "txmt://open?url=file://%%f&line=%%l"

Of course, since every developer uses a different IDE, it's better to set
this on a system level. This can be done by setting the ``xdebug.file_link_format``
PHP.ini value to the file link string. If this configuration value is set, then
the ``ide`` option does not need to be specified.

.. _reference-framework-test:

test
~~~~

**type**: ``Boolean``

If this configuration parameter is present (and not ``false``), then the
services related to testing your application (e.g. ``test.client``) are loaded.
This setting should be present in your ``test`` environment (usually via
``app/config/config_test.yml``). For more information, see :doc:`/book/testing`.

trusted_proxies
~~~~~~~~~~~~~~~

**type**: ``array``

Configures the IP addresses that should be trusted as proxies. For more details,
see :doc:`/components/http_foundation/trusting_proxies`.

.. configuration-block::

    .. code-block:: yaml

        framework:
            trusted_proxies:  [192.0.0.1]

    .. code-block:: xml

        <framework:config trusted-proxies="192.0.0.1">
            <!-- ... -->
        </framework>

    .. code-block:: php

        $container->loadFromExtension('framework', array(
            'trusted_proxies' => array('192.0.0.1'),
        ));

trust_proxy_headers
~~~~~~~~~~~~~~~~~~~

.. caution::

    The ``trust_proxy_headers`` option is deprecated and will be removed in
    Symfony 2.3. See `trusted_proxies`_ and :doc:`/components/http_foundation/trusting_proxies`
    for details on how to properly trust proxy data.

**type**: ``Boolean``

Configures if HTTP headers (like ``HTTP_X_FORWARDED_FOR``, ``X_FORWARDED_PROTO``, and
``X_FORWARDED_HOST``) are trusted as an indication for an SSL connection. By default, it is
set to ``false`` and only SSL_HTTPS connections are indicated as secure.

You should enable this setting if your application is behind a reverse proxy.

.. _reference-framework-form:

form
~~~~

csrf_protection
~~~~~~~~~~~~~~~

session
~~~~~~~

name
....

**type**: ``string`` **default**: ``null``

This specifies the name of the session cookie. By default it will use the cookie
name which is defined in the ``php.ini`` with the ``session.name`` directive.

lifetime
........

**type**: ``integer`` **default**: ``0``

This determines the lifetime of the session - in seconds. By default it will use
``0``, which means the cookie is valid for the length of the browser session.

templating
~~~~~~~~~~

assets_base_urls
................

**default**: ``{ http: [], ssl: [] }``

This option allows you to define base URLs to be used for assets referenced
from ``http`` and ``ssl`` (``https``) pages. A string value may be provided in
lieu of a single-element array. If multiple base URLs are provided, Symfony2
will select one from the collection each time it generates an asset's path.

For your convenience, ``assets_base_urls`` can be set directly with a string or
array of strings, which will be automatically organized into collections of base
URLs for ``http`` and ``https`` requests. If a URL starts with ``https://`` or
is `protocol-relative`_ (i.e. starts with `//`) it will be added to both
collections. URLs starting with ``http://`` will only be added to the
``http`` collection.

.. _ref-framework-assets-version:

assets_version
..............

**type**: ``string``

This option is used to *bust* the cache on assets by globally adding a query
parameter to all rendered asset paths (e.g. ``/images/logo.png?v2``). This
applies only to assets rendered via the Twig ``asset`` function (or PHP equivalent)
as well as assets rendered with Assetic.

For example, suppose you have the following:

.. configuration-block::

    .. code-block:: html+jinja

        <img src="{{ asset('images/logo.png') }}" alt="Symfony!" />

    .. code-block:: php

        <img src="<?php echo $view['assets']->getUrl('images/logo.png') ?>" alt="Symfony!" />

By default, this will render a path to your image such as ``/images/logo.png``.
Now, activate the ``assets_version`` option:

.. configuration-block::

    .. code-block:: yaml

        # app/config/config.yml
        framework:
            # ...
            templating: { engines: ['twig'], assets_version: v2 }

    .. code-block:: xml

        <!-- app/config/config.xml -->
        <framework:templating assets-version="v2">
            <framework:engine id="twig" />
        </framework:templating>

    .. code-block:: php

        // app/config/config.php
        $container->loadFromExtension('framework', array(
            ...,
            'templating'      => array(
                'engines'        => array('twig'),
                'assets_version' => 'v2',
            ),
        ));

Now, the same asset will be rendered as ``/images/logo.png?v2`` If you use
this feature, you **must** manually increment the ``assets_version`` value
before each deployment so that the query parameters change.

You can also control how the query string works via the `assets_version_format`_
option.

assets_version_format
.....................

**type**: ``string`` **default**: ``%%s?%%s``

This specifies a :phpfunction:`sprintf` pattern that will be used with the `assets_version`_
option to construct an asset's path. By default, the pattern adds the asset's
version as a query string. For example, if ``assets_version_format`` is set to
``%%s?version=%%s`` and ``assets_version`` is set to ``5``, the asset's path
would be ``/images/logo.png?version=5``.

.. note::

    All percentage signs (``%``) in the format string must be doubled to escape
    the character. Without escaping, values might inadvertently be interpreted
    as :ref:`book-service-container-parameters`.

.. tip::

    Some CDN's do not support cache-busting via query strings, so injecting the
    version into the actual file path is necessary. Thankfully, ``assets_version_format``
    is not limited to producing versioned query strings.

    The pattern receives the asset's original path and version as its first and
    second parameters, respectively. Since the asset's path is one parameter, you
    cannot modify it in-place (e.g. ``/images/logo-v5.png``); however, you can
    prefix the asset's path using a pattern of ``version-%%2$s/%%1$s``, which
    would result in the path ``version-5/images/logo.png``.

    URL rewrite rules could then be used to disregard the version prefix before
    serving the asset. Alternatively, you could copy assets to the appropriate
    version path as part of your deployment process and forgo any URL rewriting.
    The latter option is useful if you would like older asset versions to remain
    accessible at their original URL.

Full Default Configuration
--------------------------

.. configuration-block::

    .. code-block:: yaml

        framework:

            # general configuration
            charset:              ~
            secret:               ~ # Required
            ide:                  ~
            test:                 ~
            trust_proxy_headers:  false

            # form configuration
            form:
                enabled:              true
            csrf_protection:
                enabled:              true
                field_name:           _token

            # esi configuration
            esi:
                enabled:              true

            # profiler configuration
            profiler:
                only_exceptions:      false
                only_master_requests:  false
                dsn:                  "sqlite:%kernel.cache_dir%/profiler.db"
                username:
                password:
                lifetime:             86400
                matcher:
                    ip:                   ~
                    path:                 ~
                    service:              ~

            # router configuration
            router:
                resource:             ~ # Required
                type:                 ~
                http_port:            80
                https_port:           443

            # session configuration
            session:
                auto_start:           ~
                default_locale:       en
                storage_id:           session.storage.native
                name:                 ~
                lifetime:             0
                path:                 ~
                domain:               ~
                secure:               ~
                httponly:             ~

            # templating configuration
            templating:
                assets_version:       ~
                assets_version_format:  "%%s?%%s"
                assets_base_urls:
                    http:                 []
                    ssl:                  []
                cache:                ~
                engines:              # Required
                form:
                    resources:        [FrameworkBundle:Form]

                    # Example:
                    - twig
                loaders:              []
                packages:

                    # Prototype
                    name:
                        version:              ~
                        version_format:       ~
                        base_urls:
                            http:                 []
                            ssl:                  []

            # translator configuration
            translator:
                enabled:              true
                fallback:             en

            # validation configuration
            validation:
                enabled:              true
                cache:                ~
                enable_annotations:   false

            # annotation configuration
            annotations:
                cache:                file
                file_cache_dir:       "%kernel.cache_dir%/annotations"
                debug:                true

.. _`protocol-relative`: http://tools.ietf.org/html/rfc3986#section-4.2
