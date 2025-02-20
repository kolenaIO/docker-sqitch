Sqitch Docker Packaging
=======================

Kolena
------

Steps for building Kolena sqitch images:

```sh
for py_version in 3.7 3.8 3.9 3.10 3.11
do
    env PY_VERSION=${py_version} ./build --build-arg sf_account=kolena-eng
    docker push ghcr.io/kolenaio/docker-sqitch/sqitch:1.1.0-py${py_version}
    env DIR=snowflake PY_VERSION=${py_version} ./build --build-arg sf_account=kolena-eng
    docker push ghcr.io/kolenaio/docker-sqitch/sqitch:1.1.0-snowflake-py${py_version}
done
```

Synopsis
--------

### Linux, macOS, Git Bash

```sh
docker pull sqitch/sqitch
curl -L https://git.io/JJKCn -o sqitch && chmod +x sqitch
./sqitch help
```

### Windows

```bat
docker pull sqitch/sqitch
WebRequest -Uri "https://git.io/JTAi6" -OutFile "sqitch.bat"
.\sqitch help
```

Description
-----------

This project is the source for creating the official [Sqitch Project] Docker
Image. It's built on [Debian bookworm-slim] in an effort to keep the image as
small as possible while supporting all known engines. It includes support for
managing [PostgreSQL], [CockroachDB], [YugabyteDB], [SQLite], [MariaDB]
([MySQL]), and [Firebird] databases, and other images may be built to support
for the other database engines that Sqitch supports.

Notes
-----

*   The [`docker-sqitch.sh`] shell script is the easiest way to run Sqitch from
    a Docker image. The script mounts the current directory and the home
    directory, so that it acts on the Sqitch project in the current directory
    and reads configuration from the home directory almost as if it was running
    natively on the local host. It also copies over most of the environment
    variables that Sqitch cares about, for transparent configuration.
*   The [`docker-sqitch.bat`] batch script is an alternative for running Sqitch
    on Windows. Modeled on [`docker-sqitch.sh`], it also detects environment
    variables and mounts the project and home directories. Support for
    configuration files will vary by database client, as many on Windows
    differ from their *unix counterparts and won't map into the container home
    directory.
*   By default, the container runs as the `sqitch` user. On macOS and Windows
    this works well, as files created by Sqitch in the working directory on the
    host will be properly owned by the host user. On Linux, however,
    [`docker-sqitch.sh`] runs the container with the UID and GID of the current
    host user, again so that files created by Sqitch will be owned by that user.
    If you find that the container cannot access configuration files in your
    home directory or write change scripts to the local directory, run
    `sudo docker-sqitch.sh` to run as the root user. Just be sure to `chown`
    files that Sqitch created for the consistency of your project.
*   If your engine falls back on the system username when connecting to the
    database (as the PostgreSQL engine does), you will likely want to set the
    username in sqitch target URIs, or set the proper [environment variables] to
    fall back on. Database authentication failures for the usernames `sqitch` or
    `root` are the hint you'll want to look for.
*   If you need to connect to a database server on your local host (or running
    in a container with the listening port mapped to the local host), use the
    host name `host.docker.internal` instead of `localhost`. Connections should
    work transparently when running Docker on Windows or macOS, although not yet
    on Linux (watch [this PR](https://github.com/docker/libnetwork/pull/2348)
    for it to land). In the meantime you can [use a NAT gateway
    container](https://github.com/qoomon/docker-host) to forward traffic to the
    Docker host.
*   Custom images for [Oracle], [Snowflake], or [Exasol] can be built by
    downloading the appropriate binary files and using the `Dockerfile`s in the
    appropriately-named subdirectories of this repository.
*   The Sqitch image is built with AMD64 and ARM64 support, but the [Oracle],
    [Snowflake] and [Exasol] images can currently only be built for AMD64.
*   In an effort to keep things as simple as possible, the only editor included
    and configured for use in the image is [nano]. This is a very simple, tiny
    text editor suitable for editing change descriptions and the like. Its
    interface always provides menus to make it easy to figure out how to use it.
    If you need another editor, this image isn't for you, but you can create
    one based on this image and add whatever editors you like.

  [Sqitch Project]: https://sqitch.org
  [Debian bookworm-slim]: https://hub.docker.com/_/debian/tags?name=bookworm-slim
  [PostgreSQL]: https://postgresql.org
  [YugabyteDB]: https://www.yugabyte.com/yugabytedb/
  [CockroachDB]: https://www.cockroachlabs.com/product/
  [SQLite]: https://sqlite.org/
  [MariaDB]: https://mariadb.com/
  [MySQL]: https://mysql.com/
  [Firebird]: https://www.firebirdsql.org
  [`docker-sqitch.sh`]: https://git.io/JJKCn
  [environment variables]: http://metacpan.org/pod/sqitch-environment
  [Oracle]: https://www.oracle.com/database/
  [Snowflake]:https://www.snowflake.com
  [Exasol]:https://www.exasol.com/
  [nano]: https://www.nano-editor.org/
