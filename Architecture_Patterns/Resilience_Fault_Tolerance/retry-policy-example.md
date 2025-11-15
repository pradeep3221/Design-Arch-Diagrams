# Retry Policy Example

Example YAML for a retry policy in a microservice:

```yaml
retry:
  maxAttempts: 5
  delay: 200ms
  backoff: exponential
```
