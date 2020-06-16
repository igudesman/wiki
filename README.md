Unit testing with publishing in SonarQube
=========================================

Setting up environment
----------------------

Install requirements:

    $ pip3 install -r requirements.txt

Install buildx:

`buildx` comes bundled with Docker CE starting with 19.03, but requires experimental mode to be enabled on the Docker CLI.
To enable it, `"experimental": "enabled"` can be added to the CLI configuration file `~/.docker/config.json`. An alternative is to set the `DOCKER_CLI_EXPERIMENTAL=enabled` environment variable.

Download the latest binary release from https://github.com/docker/buildx/releases/latest and copy it to `~/.docker/cli-plugins` folder with name `docker-buildx`.

Change the permission to execute:

    $ chmod a+x ~/.docker/cli-plugins/docker-buildx
    
Run *Apache Airflow* and *PostgreSQL*

Run
---
Run application:
    from uploadservice folder::

    $ db_host=localhost AIRFLOW_HOST=xx.xx.xx.xx AIRFLOW_PORT=8080 UPLOADSERVICE_PORT=8081 python3 -m service


Unit tests
----------

from uploadservice folder::

    $ pytest unittests


Make docker image
-----------------

from uploadservice folder::

    $ docker build -t registry.stageogip.ru/ksdm/ksdm-uploadservice:latest .

Run integration-tests
---------------------

from uploadservice folder::

    $ docker-compose -f ../docker-compose.yml up --force-recreate

    $ ./integration-tests/run_tests.sh

exit 1 if failed

Run application in container
----------------------------

After starting container run command
(docker exec upload_service /bin/sh -c  /wait) && (docker exec -d upload_service /bin/sh -c  /app/run.sh)
By using fake EntryPoint - there is availability replace all files in running container and restart service by executing /app/run.sh
