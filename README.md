Uploadservice for KSDM project
==============================

Preparations
------------
Python version: 3.7

Install requirements:
    $ pip3 install -r requirements.txt


Run Postgres DB server::

    $ docker run --rm -it -p 5432:5432 postgres:10

Create db and populate it with sample data::

    $ python init_db.py




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
