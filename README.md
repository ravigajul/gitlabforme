# gitlabforme
This is to capture my gitlab learnings

## Artifacts
The jobs in gitlab execute independently from one another. They do not exchange data if not told.This can be achieved to artifacts.
```yaml
artifacts:
  paths:
    - /<directory>
```
