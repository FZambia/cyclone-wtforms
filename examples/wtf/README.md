# cyclone-based project

	This is the source code of wtf
	Foo Bar <root@localhost>

## About

This file has been created automatically by cyclone-tool for wtf.
It contains the following files:

- ``start.sh``: simple shell script to start the server
- ``wtf.conf``: configuration file for the web server
- ``wtf/__init__.py``: information such as author and version of this package
- ``wtf/web.py``: map of url handlers and main class of the web server
- ``wtf/config.py``: configuration parser for ``wtf.conf``
- ``wtf/views.py``: code of url handlers for the web server
- ``scripts/debian-init.d``: generic debian start/stop init script
- ``scripts/debian-multicore-init.d``: run one instance per core on debian
- ``scripts/localefix.py``: script to fix html text before running ``xgettext``
- ``scripts/cookie_secret.py``: script for generating new secret key for the web server

### Running

For development and testing:

    twistd -n cyclone --help
    twistd -n cyclone -r wtf.web.Application [--help]

For production:

    twistd cyclone \
    	   --logfile=/var/log/wtf.log \
    	   --pidfile=/var/run/wtf.pid \
	   -r wtf.web.Application


### Convert this document to HTML

Well, since this is a web server, it might be a good idea to convert this document
to HTML before getting into customization details.

This can be done using [markdown](http://daringfireball.net/projects/markdown/).

	brew install markdown
	markdown README.md > frontend/static/readme.html

And point your browser to <http://localhost:8888/static/readme.html> after this server
is running.

## Customization

This section is dedicated to explaining how to customize your brand new package.

### Databases

cyclone provides built-in support for SQLite and Redis databases.
It also supports any RDBM supported by the ``twisted.enterprise.adbapi`` module,
like MySQL or PostgreSQL.

The default configuration file ``wtf.conf`` ships with pre-configured
settings for SQLite, Redis and MySQL.

The code for loading all the database settings is in ``wtf/config.py``.
Feel free to comment or even remove such code, and configuration entries. It
shouldn't break the web server.

Take a look at ``wtf/utils.py``, which is where persistent database
connections are initialized.


### Internationalization

cyclone uses the standard ``gettext`` library for dealing with string
translation.

Make sure you have the ``gettext`` package installed. If you don't, you won't
be able to translate your software.

For installing the ``gettext`` package on Debian and Ubuntu systems, do this:

    apt-get install gettext

For Mac OS X, I'd suggest using [HomeBrew](http://mxcl.github.com/homebrew>).
If you already use HomeBrew, run:

    brew install gettext
    brew link gettext

For generating translatable files for HTML and Python code of your software,
run this:

    cat frontend/template/*.html wtf/*.py | python scripts/localefix.py | \
        xgettext - --language=Python --from-code=utf-8 --keyword=_:1,2 -d wtf

Then translate wtf.po, compile and copy to the appropriate locale
directory:

    (pt_BR is used as example here)
    vi wtf.po
    mkdir -p frontend/locale/pt_BR/LC_MESSAGES/
    msgfmt wtf.po -o frontend/locale/pt_BR/LC_MESSAGES/wtf.mo

There are sample translations for both Spanish and Portuguese in this package,
already compiled.


### Cookie Secret

The current cookie secret key in ``wtf.conf`` was generated during the
creation of this package. However, if you need a new one, you may run the
``scripts/cookie_secret.py`` script to generate a random key.

## Credits

- [cyclone](http://github.com/fiorix/cyclone) web server.
