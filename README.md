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
    - curl "http://localhost:9000" | grep -q "Gatsby"
```
