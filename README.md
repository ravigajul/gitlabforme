# gitlabforme
This is to capture my gitlab learnings

## Artifacts
The jobs in gitlab execute independently from one another. They do not exchange data if not told.This can be achieved to artifacts.
```yaml
stages:
    - build
    - test

build:
    stage: build
    script:
        - echo "Building the project"
        - mkdir testproj
        - touch testproj/info.txt
    artifacts:
        paths:
            - testproj/

test:
    stage: test
    script:
        - echo "Testing"
        - test -f "testproj/info.txt"
```
## Gitlab Runner to use a specific image
Git lab runner uses default docker images like ruby if not specified. Everything that is run in the below image will be destroyed as the gitlab runner runs this in the docker container with below image that gets destroyed immediately after the intended task is done.
```yaml
Test website:
  image: node:lts
  script:
    - npm install
    - npx cypress run
  artifacts:
    paths:
      - ./Reports
```
## Job Status codes
0 - Successfull <br>
1-225 - Error....Failure

## Running grep in silent mode
```linux
grep -q "testing" test.html
```

## Get the status code of previous command
```linux
echo $?
```
## Running Jobs in parallel
When the jobs are created with same stagename, they will be set to run in parallel
```yml
stages:
  - build
  - test

build website:
  stage: build
  image: node
  script:
    - npm install
    - npm install -g gatsby-cli
    - gatsby build
  artifacts:
    paths:
      - ./public

test artifact:
  image: alpine
  stage: test
  script:
    - grep -q "Gatsby" ./public/index.html

test website:
  image: node
  stage: test
  script:
    - npm install
    - npm install -g gatsby-cli
    - gatsby serve &
    - sleep 3
    - curl "http://localhost:9000" |tac|tac| grep -q "Gatsby"
```
tac is a simple unix program that reads the entire input page and reverses the line order(hence using twice)

##  Using Caches to speed up the execution of the job
cache can be cleared by clicking clear cache on the ui. Adding caches ensures that node_modules in this case will not be redownloaded but uses it from cached version
```yml
stages:
  - build
  - test
  - deploy
  - deployment tests

cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - node_modules/
```
## Environment Variables
Environment variables play an important role. For instance in the below config, we get to see the two enviroments(CICD->Environments). prod and staging and have the possibility of directly navigating to the urls mentioned
```yml
deploy staging: 
  stage: deploy staging
  environment:
    name: staging
    url: http://test-staging.com
  script:
    - <your deployment script>
```
## Global Variables
User Defined variables can be defined at global level using variables keyword
```yml
Variables:
    - UAT: test.uat.com
    - PROD: prod.com
```
Environment variables in GitLab CI/CD pipelines can also be defined outside the .gitlab-ci.yml file. There are several ways to do this:
1. Project/Group variables: You can define variables at the project or group level in the GitLab web interface. These variables are available to all pipelines in the project or group and can be accessed by using the syntax $VAR_NAME.<br>
2. Secret variables: You can define secret variables at the project or group level in the GitLab web interface. Secret variables are similar to regular variables, but their values are encrypted and can only be decrypted by GitLab at runtime. They can be accessed by using the syntax $VAR_NAME.<br>
3. File-based variables: You can also store your environment variables in a separate file and then reference them in your .gitlab-ci.yml file. To use this method, you would need to store the variables in a file (e.g. env.txt), and then use the env keyword in your .gitlab-ci.yml file to load the variables. For example:<br>
```yml
variables:
  env: env.txt

stages:
  - build

build:
  stage: build
  script:
    - echo "Building with the following environment variables:"
    - echo "$VAR_NAME1: $VAR_NAME1"
    - echo "$VAR_NAME2: $VAR_NAME2"
```
In this example, the env.txt file would contain a list of environment variables in the following format:<br>
```text
VAR_NAME1=value1
VAR_NAME2=value2
```
## Configuring Manual Triggers
Manual triggers can be configured by using when keyword just before the script label
```yml
when: manual
script:
    - <your script>
```

## Prevent the subsequent stages to not run of block upon the previous stage failure or if it is waiting for manual trigger
```yml
when: manual
allow_failure: false
script:
    - <your script>
```

