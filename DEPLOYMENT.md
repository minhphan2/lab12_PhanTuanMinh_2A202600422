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
- [Dashboard](https://github.com/minhphan2/lab12_PhanTuanMinh_2A202600422/blob/main/screenshots/lab12deploy%20dashboard.png)
- [Health Check](https://github.com/minhphan2/lab12_PhanTuanMinh_2A202600422/blob/1a957fed573f2c363dfedff53540ef8b2d9357e3/screenshots/lab12deploy%20health.png)
- [API Docs](https://github.com/minhphan2/lab12_PhanTuanMinh_2A202600422/blob/1a957fed573f2c363dfedff53540ef8b2d9357e3/screenshots/lab12deploydocs.png)
