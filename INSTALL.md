INSTALLATION INSTRUCTIONS FOR GREENBONE SECURITY ASSISTANT
==========================================================

Please note: The reference system used by most of the developers is Debian
GNU/Linux 'Buster' 10. The build might fail on any other system. Also, it is
necessary to install dependent development packages.


Prerequisites for Greenbone Security Assistant
----------------------------------------------

See at the end of this section how to easily install these prerequisites on
some supported platforms.

Prerequisites:
* libgvm_base, libgvm_util, libgvm_gmp >= 10.0.0
* gnutls >= 3.2.15
* libgcrypt
* cmake >= 3.0
* glib-2.0 >= 2.42
* libxml
* libmicrohttpd >= 0.9.0
* pkg-config

Prerequisites for using translations:
* gettext
  (when building from source)
* an installed English UTF-8 locale (e.g. `en_US.UTF8`, `en_GB.UTF8`)
  (See "Setting up translations")

Prerequisites for building documentation:
* Doxygen
* xmltoman (optional, for building man page)

Prerequisites to work on the translations from C based sources and
also to build the JavaScript translations:
* python-polib

Install prerequisites on Debian GNU/Linux:

    apt-get install libmicrohttpd-dev libxml2-dev


Compiling Greenbone Security Assistant
--------------------------------------

If you have installed required libraries to a non-standard location, remember to
set the `PKG_CONFIG_PATH` environment variable to the location of you pkg-config
files before configuring:

    export PKG_CONFIG_PATH=/your/location/lib/pkgconfig:$PKG_CONFIG_PATH

Create a build directory and change into it with:

    mkdir build
    cd build

Then configure the build with:

    cmake -DCMAKE_INSTALL_PREFIX=/path/to/your/installation ..

Or (if you want to use the default installation path /usr/local):

    cmake ..

This only needs to be done once.

Thereafter, the following commands are useful:

    make                # build the scanner
    make doc            # build the documentation
    make doc-full       # build more developer-oriented documentation
    make install        # install the build
    make rebuild_cache  # rebuild the cmake cache

Please note that you may have to execute `make install` as root, especially if
you have specified a prefix for which your user does not have full permissions.

To clean up the build environment, simply remove the contents of the `build`
directory you created above.

In case you have installed the Greenbone Security Assistant into a path
different from the other GVM modules, you might need to set some paths
explicitly before running `cmake`. The certificate and key locations in
`GVM_SERVER_CERTIFICATE`, `GVM_SERVER_KEY` and `GVM_CA_CERTIFICATE` in
the top-level CMakeLists.txt may need modifying.


Logging Configuration
---------------------

By default, GSA writes logs to the file

    <install-prefix>/var/log/gvm/gsad.log

Logging is configured entirely by the file

    <install-prefix>/etc/gvm/gsad_log.conf

The configuration is divided into domains like this one

    [gsad main]
    prepend=%t %p
    prepend_time_format=%Y-%m-%d %Hh%M.%S %Z
    file=/var/log/gvm/gsad.log
    level=128

The `level` field controls the amount of logging that is written.
The value of `level` can be:

      4  Errors.
      8  Critical situation.
     16  Warnings.
     32  Messages.
     64  Information.
    128  Debug.  (Lots of output.)

Enabling any level includes all the levels above it. So enabling Information
will include Warnings, Critical situations and Errors.

To get absolutely all logging, set the level to 128 for all domains in the
configuration file.

Logging to `syslog` can be enabled in each domain like:

    [gsad main]
    prepend=%t %p
    prepend_time_format=%Y-%m-%d %Hh%M.%S %Z
    file=syslog
    syslog_facility=daemon
    level=128


Static code analysis with the Clang Static Analyzer
---------------------------------------------------

If you want to use the Clang Static Analyzer (https://clang-analyzer.llvm.org/)
to do a static code analysis, you can do so by prefixing the configuration and
build commands with `scan-build`:

    scan-build cmake ..
    scan-build make

The tool will provide a hint on how to launch a web browser with the results.

It is recommended to do this analysis in a separate, empty build directory and
to empty the build directory before `scan-build` call.


Specifying Diffie-Hellman parameters file
---------------------------------------------------

For the value of `--dh-params` to take effect, LibmicroHTTPD version 0.9.35 or
higher is required.