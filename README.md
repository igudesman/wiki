Unit testing with publishing in SonarQube
=========================================

General information
-------------------
Following instructions allow you to run unit tests for uploadservice and publish results (code coverage and tests results) in SonarQube.

*run_pytest.sh* - script runs pytest and generates *report.xml*, *result.xml*, *status.txt*
*report.xml* - code coverage for SonarQube and TFS
*result.xml* - tests results for SonarQube and TFS
*status.txt* - file with exit status for *run_pytest.sh*

Setting up environment for local run
------------------------------------

**Install requirements:**

    $ pip3 install -r requirements.txt

**Install buildx:**

`buildx` comes bundled with Docker CE starting with 19.03, but requires experimental mode to be enabled on the Docker CLI.
To enable it, `"experimental": "enabled"` can be added to the CLI configuration file `~/.docker/config.json`. An alternative is to set the `DOCKER_CLI_EXPERIMENTAL=enabled` environment variable.

Download the latest binary release from https://github.com/docker/buildx/releases/latest and copy it to `~/.docker/cli-plugins` folder with name `docker-buildx`.

Change the permission to execute:

    $ chmod a+x ~/.docker/cli-plugins/docker-buildx
   
Buildx documentation: https://github.com/docker/buildx

**Install sonar-scanner:**

```
instalation_dir=./sonar-scanner
if [ -d $instalation_dir ];then
    mkdir -p $instalation_dir
fi

echo "########## INSTALLING SONAR SCANNER ##########"
printf "Downloading... "
wget -q https://binaries.sonarsource.com/Distribution/sonar-scanner-cli/sonar-scanner-cli-4.3.0.2102-linux.zip
printf "Complete!\n"

printf "Unpacking file... "
unzip -q sonar-scanner-cli-4.3.0.2102-linux.zip
rm sonar-scanner-cli-4.3.0.2102-linux.zip
printf "Complete!\n"

echo "Installing on "$instalation_dir
mv sonar-scanner-4.3.0.2102-linux $instalation_dir

echo "Adding Sonar Scanner to PATH..."
export PATH=$PATH:sonar-scanner/bin

#cp -f sonar-scanner.properties $instalation_dir/conf/sonar-scanner.properties

echo "Installation Complete."
echo "---------------------------------------"
cat sonar-scanner.properties
echo "---------------------------------------"
```
For more information: https://docs.sonarqube.org/latest/analysis/scan/sonarscanner/
    
**Configure sonar-scanner.properties in unittests folder:**

For more detailed configuration: https://docs.sonarqube.org/latest/analysis/analysis-parameters/
    
**Run *Apache Airflow* and *PostgreSQL***

Local Run
---------
from KSDM folder:

    $ chmod +x uploadservice/unittests/run_pytest.sh 
    $ sh uploadservice/unittests/run_pytest.sh
    $ sonar-scanner -Dproject.settings=uploadservice/unittests/sonar-scanner.properties

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
