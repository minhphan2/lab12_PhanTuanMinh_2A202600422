# Deployment Information

## Public URL
https://minhai-production.up.railway.app

## Platform
Railway

## Test Commands

### Health Check
```bash
curl https://minhai-production.up.railway.app/health
# Expected: {"status": "ok", ...}
```

### API Test (with authentication)
```bash
curl -X POST https://minhai-production.up.railway.app/ask \
  -H "X-API-Key: my-secret-key-123" \
  -H "Content-Type: application/json" \
  -d '{"question": "What is Docker?"}'
```

## Environment Variables Set
- PORT=8000
- AGENT_API_KEY=my-secret-key-123

## Screenshots
- [Dashboard](screenshots/lab12 1.png)
- [Health Check](screenshots/lab12deploy hea)
- [API Docs](screenshots/lab12 3.png)
