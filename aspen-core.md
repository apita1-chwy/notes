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

## Jenkins Jobs

### ads-cm-ui

- Build: https://jenkins-nonprod.shss.chewy.com/job/build-ads-cm-ui/
- Deploy to low envs: https://jenkins-nonprod.shss.chewy.com/job/deploy-ads-cm-ui-stg/

### aspen-core

- Build: https://jenkins-nonprod.shss.chewy.com/job/build-aspen-core/
- Deploy to low envs: https://jenkins-nonprod.shss.chewy.com/job/deploy-aspen-core-campaign-api-stg/

## Swagger

Aspen Core swagger docs (stg):
https://ads-stg.chewy.net/api/campaign/swagger-ui/index.html#/

## Local Setup

To run locally, login to AWS:

```
aws-sso-util login
```

Choose user: `PowerUserAccess`.

To run Aspen Core, use IntelliJ Gradle task:

`campaignservice -> Tasks -> application -> run`

To start the application, start the Postgres app to launch the database.

### Run Reporting Service Locally

`reportingservice` lives inside the Aspen Core repo and returns reporting data.

From `/Users/apita1/repos/aspen-core`, run:

```bash
cd /Users/apita1/repos/aspen-core
export JAVA_HOME=$(/usr/libexec/java_home -v 21)
./gradlew clean :reportingservice:build
./gradlew :reportingservice:bootRun
```

To run on a different port (for example `8081`), use:

```bash
SERVER_PORT=8081 ./gradlew :reportingservice:bootRun
```

To validate it is running, open:

http://localhost:8080/swagger-ui/index.html#/

If using `SERVER_PORT=8081`, validate at:

http://localhost:8081/swagger-ui/index.html#/

## Troubleshooting

If you see “unable to instantiate bean” errors in Aspen Core, clean IntelliJ cache:

`File -> Invalidate Caches...`

## Migrations (Flyway)

To run Aspen Core migrations locally, edit `campaignservice/build.gradle`:

```gradle
plugins {
    id 'buildlogic.java-application-conventions'
    id "io.freefair.lombok" version "8.6"
    id 'org.flywaydb.flyway' version '9.22.3'
}
// flyway config
flyway {
    url = 'jdbc:postgresql://localhost:5432/reporting' // Set your DB connection
    user = 'root'
    // Specify the external migrations folder
    locations = ["filesystem:/Users/apita1/repos/aspen-core/flyway/sql"]
}
```

Then run the Gradle task:

`campaignservice -> Tasks -> flyway -> flywayMigrate`

If DB migrations don't match code, delete the mismatched rows from `flyway_schema_history`, then rerun `flywayMigrate`.
