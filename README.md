Haskell Cloud
=============

Haskell Cloud is an [OpenShift](https://www.openshift.com/) cartridge for deploying Haskell on the open source PaaS cloud. It includes:

- GHC
- cabal-install
- gold linker

OpenShift
---------
For a general understanding of the OpenShift environment, consult the [Online](https://access.redhat.com/site/documentation/en-US/OpenShift_Online/2.0/html/User_Guide/) or [Origin](http://openshift.github.io/documentation/oo_user_guide.html) user guides.

Installation
------------
Haskell Cloud is built in various flavours, with different pre-installed packages. See the [Haskell wiki](http://www.haskell.org/haskellwiki/Web/Cloud) for details and installation links.

Haskell
-------
The application's `cabal` file must define an executable called `server`, which takes two command line arguments; the IP address and port number to listen on. (These can also be take from `$OPENSHIFT_HASKELL_IP` and `$OPENSHIFT_HASKELL_PORT`.) When new code is pushed to the application's repository, the cartridge will build it with `cabal install`, then start the server. The server will be sent the `SIGTERM` signal when the cartridge receives the stop command.

Paths_
------
Cabal's autogenerated `Paths_` module is likely to return the wrong paths, as the program may not be running on the same gear that built it ([#462](https://github.com/haskell/cabal/issues/462), [#1542](https://github.com/haskell/cabal/issues/1542)). Use the relevant [Openshift environment variables](https://www.openshift.com/page/openshift-environment-variables) instead.

Build Tools
-----------
Cabal does not automatically install `build-tools` ([#220](https://github.com/haskell/cabal/issues/220)). They can be installed by temporarily adding them to `build-depends`. If they are installed directly on the server over ssh, any absolute paths in the database entries (`.conf`) of newly installed packages should be made relative to `${pkgroot}`.

Cabal Update
------------
The `cabal_update` marker (see below) will run `cabal update` before every build. Ad-hoc updates can be performed with `rhc ssh <app> 'cabal update'`.

Logging
-------
`stdout` and `stderr` are logged to `$OPENSHIFT_HASKELL_LOG_DIR` (remember to `hFlush stdout` after each log message, or `hSetBuffering stdout LineBuffering`). Other logs may be written to `$OPENSHIFT_HASKELL_LOG_DIR` as desired.

Tidying
-------
OpenShift's `tidy` command will delete all logs (if the server is stopped), cabal's cache of downloaded packages, and the repository working directory. Installed packages (and binaries) are not deleted.

Markers
-------
Markers can be created in `.openshift/markers` to modify the build process. See [README](template/.openshift/markers/README) for details.
