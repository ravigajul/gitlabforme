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
