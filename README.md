# Robot Framework in Docker, with Firefox, Chrome and Microsoft Edge

* [Robot Framework](https://github.com/robotframework/robotframework) 7.0
* [Robot Framework Browser (Playwright) Library](https://github.com/MarketSquare/robotframework-browser) 18.0.0
* [Robot Framework DatabaseLibrary](https://github.com/franz-see/Robotframework-Database-Library) 1.4.3
* [Robot Framework Datadriver](https://github.com/Snooz82/robotframework-datadriver) 1.10.0
* [Robot Framework DateTimeTZ](https://github.com/testautomation/DateTimeTZ) 1.0.6
* [Robot Framework Faker](https://github.com/guykisel/robotframework-faker) 5.0.0
* [Robot Framework FTPLibrary](https://github.com/kowalpy/Robot-Framework-FTP-Library) 1.9
* [Robot Framework IMAPLibrary 2](https://pypi.org/project/robotframework-imaplibrary2/) 0.4.6
* [Robot Framework Pabot](https://github.com/mkorpela/pabot) 2.18.0
* [Robot Framework Requests](https://github.com/bulkan/robotframework-requests) 0.9.6
* [Robot Framework SeleniumLibrary](https://github.com/robotframework/SeleniumLibrary) 6.2.0
* [Robot Framework SSHLibrary](https://github.com/robotframework/SSHLibrary) 3.8.0
* [Axe Selenium Library](https://github.com/mozilla-services/axe-selenium-python) 2.1.6
* Firefox 123.0
* Chromium 122.0
* Microsoft Edge 121.0.2277.106
* [Amazon AWS CLI](https://pypi.org/project/awscli/) 1.32.36

<a name="running-the-container"></a>

## Running the container


    docker run \
        -v <local path to the reports' folder>:/opt/robotframework/reports:Z \
        -v <local path to the test suites' folder>:/opt/robotframework/tests:Z \
        ppodgorsek/robot-framework:<version>

<a name="switching-browsers"></a>

### Changing the container's tests and reports directories

It is possible to use different directories to read tests from and to generate reports to. This is useful when using a complex test file structure. To change the defaults, set the following environment variables:

* `ROBOT_REPORTS_DIR` (default: /opt/robotframework/reports)
* `ROBOT_TESTS_DIR` (default: /opt/robotframework/tests)

<a name="parallelisation"></a>

### Passing additional options

RobotFramework supports many options such as `--exclude`, `--variable`, `--loglevel`, etc. These can be passed by using the `ROBOT_OPTIONS` environment variable, for example:

    docker run \
        -e ROBOT_OPTIONS="--loglevel DEBUG" \
        ppodgorsek/robot-framework:latest

<a name="testing-emails"></a>

### Installing additional dependencies

It is possible to install additional dependencies dynamically at runtime rather than having to extend this image.

The file must follow [Pip's official requirements file format](https://pip.pypa.io/en/stable/reference/requirements-file-format/).

```
robotframework-docker==1.4.2
rpa==1.50.0
```

## Continuous integration

It is possible to run the project from within a Jenkins pipeline by relying on the shell command line directly:

    pipeline {
        agent any
        stages {
            stage('Functional regression tests') {
                steps {
                    sh "docker run --shm-size=1g -e BROWSER=firefox -v $WORKSPACE/robot-tests:/opt/robotframework/tests:Z -v $WORKSPACE/robot-reports:/opt/robotframework/reports:Z ppodgorsek/robot-framework:latest"
                }
            }
        }
    }

The pipeline stage can also rely on a Docker agent, as shown in the example below:

    pipeline {
        agent none
        stages {
            stage('Functional regression tests') {
                agent { docker {
                    image 'ppodgorsek/robot-framework:latest'
                    args '--shm-size=1g -u root' }
                }
                environment {
                    BROWSER = 'firefox'
                    ROBOT_TESTS_DIR = "$WORKSPACE/robot-tests"
                    ROBOT_REPORTS_DIR = "$WORKSPACE/robot-reports"
                }
                steps {
                    sh '''
                        /opt/robotframework/bin/run-tests-in-virtual-screen.sh
                    '''
                }
            }
        }
    }

<a name="defining-a-test-run-id"></a>

## Testing this project

Not convinced yet? Simple tests have been prepared in the `test/` folder, you can run them using the following commands:

    # Using Chromium
    docker run \
        -v `pwd`/reports:/opt/robotframework/reports:Z \
        -v `pwd`/test:/opt/robotframework/tests:Z \
        -e BROWSER=chrome \
        ppodgorsek/robot-framework:latest

    # Using Firefox
    docker run \
        -v `pwd`/reports:/opt/robotframework/reports:Z \
        -v `pwd`/test:/opt/robotframework/tests:Z \
        -e BROWSER=firefox \
        ppodgorsek/robot-framework:latest

For Windows users who use **PowerShell**, the commands are slightly different:

    # Using Chromium
    docker run \
        -v ${PWD}/reports:/opt/robotframework/reports:Z \
        -v ${PWD}/test:/opt/robotframework/tests:Z \
        -e BROWSER=chrome \
        ppodgorsek/robot-framework:latest

    # Using Firefox
    docker run \
        -v ${PWD}/reports:/opt/robotframework/reports:Z \
        -v ${PWD}/test:/opt/robotframework/tests:Z \
        -e BROWSER=firefox \
        ppodgorsek/robot-framework:latest

Screenshots of the results will be available in the `reports/` folder.

<a name="troubleshooting"></a>
