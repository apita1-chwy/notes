# Dynatrace

## Search Logs in Aspen Core API

```
fetch logs
| filter matchesValue(log.source, "aspen-core-campaign-api")
| filter contains(content, "OffsiteServiceClient")
| filter contains(content, "4927c446-4943-4f9e-a64a-58d6a9902463")
| sort timestamp desc
```

Filter by log source, search for specific content strings, and sort by timestamp descending.

## Common Search Patterns

**Request body:**
```
"Offsite service http request method"
```

**Errors:**
```
"Offsite service {} operation failed with status"
```

**Successful response logs:**
```
"Successfully {}d offsite campaign for request id {}"
```

**Offsite errors:**
```
"Offsite service create operation failed with status"
```
