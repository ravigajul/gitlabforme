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
