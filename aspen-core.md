# Aspen Core

## Database Queries

### Check Child Campaign by Name

```sql
select * from child_campaigns cc 
where name = 'Applaws_127_featureflagdisabled_isnameupdatesupported'
```

## Dev Deploy Notes

### Run Aspen Core in Low Env (Dev)

Add these lines at the end of `aspen-core/build.gradle`:

```
tasks.devSnapshot.finalizedBy(':campaignservice:dockerPushImage')
tasks.devSnapshot.finalizedBy(':e2etesting:dockerBuildImage')
tasks.devSnapshot.finalizedBy(':flyway:dockerPushImage')
```

Then push the change and do the feature branch deploy.

Rationale: the pipeline is missing the Docker container image in ECR, so the dev instance deploy fails without these tasks.
