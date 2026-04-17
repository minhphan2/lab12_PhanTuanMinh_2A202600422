# Day 12 Lab - Mission Answers

> **Student Name:** Phan Tuấn Minh  
> **Student ID:** 2A202600422  
> **Date:** 17/04/2026

---

## Part 1: Localhost vs Production

### Exercise 1.1: Anti-patterns found

5 vấn đề tìm được trong `01-localhost-vs-production/develop/app.py`:

1. **Hardcode API key** (dòng 17): `OPENAI_API_KEY = "sk-hardcoded-fake-key-never-do-this"` — nếu push lên GitHub, hacker lấy key ngay, mất tiền OpenAI.
2. **Hardcode Database URL với password** (dòng 18): `DATABASE_URL = "postgresql://admin:password123@localhost:5432/mydb"` — lộ toàn bộ thông tin database.
3. **Dùng `print()` thay vì logging + log ra secret** (dòng 33-34): `print(f"[DEBUG] Using key: {OPENAI_API_KEY}")` — secret xuất hiện trong log, không filter được theo level.
4. **Không có Health Check endpoint** (dòng 42): Cloud platform (Railway, Render) không biết agent crash → không tự restart được.
5. **Port cứng + host localhost + reload=True** (dòng 49-54): `host="localhost"` chỉ chạy trên máy local, không nhận kết nối từ bên ngoài container. Port cứng 8000 trong khi cloud platform inject PORT qua env var. `reload=True` gây overhead trong production.

### Exercise 1.3: Comparison table

| Feature | Develop (Basic) | Production (Advanced) | Tại sao quan trọng? |
|---------|----------------|----------------------|---------------------|
| Config | Hardcode trong code | Environment variables (`.env` + `config.py`) | Dễ thay đổi giữa dev/staging/prod mà không sửa code |
| Health check |  Không có |  `GET /health` + `GET /ready` | Platform biết khi nào cần restart, LB biết instance nào sẵn sàng |
| Logging | `print()` — log cả secret | JSON structured logging — không log secret | Dễ parse bằng Datadog/Loki, bảo mật thông tin nhạy cảm |
| Shutdown | Đột ngột — request đang xử lý bị mất | Graceful — chờ request hoàn thành rồi mới tắt | Không mất data, user không bị lỗi giữa chừng |
| Host binding | `localhost` — chỉ local | `0.0.0.0` — nhận kết nối từ bên ngoài | Container/cloud cần truy cập từ ngoài vào |
| Port | Cứng `8000` | Từ `PORT` env var | Railway/Render inject port tự động |
| CORS |  Không có |  Configured với allowed origins | Bảo mật cross-origin requests |
| Input validation | Không validate | Validate `question` field | Tránh crash từ input xấu |

---

## Part 2: Docker

### Exercise 2.1: Dockerfile questions

1. **Base image là gì?** — `python:3.11`
2. **Working directory là gì?** — `/app` ,set bằng `WORKDIR /app`
3. **Tại sao COPY requirements.txt trước?** — Tận dụng **Docker layer cache**. Nếu requirements.txt không đổi, Docker không cần cài lại dependencies giúp build nhanh hơn nhiều. Chỉ khi code thay đổi mới build lại phía sau.
4. **CMD vs ENTRYPOINT khác nhau thế nào?**
   - `CMD`: command mặc định, có thể bị override khi `docker run`
   - `ENTRYPOINT`: command cố định, `docker run` chỉ thêm arguments
   - Ví dụ: `CMD ["python", "app.py"]`, chạy `docker run image bash` sẽ override thành bash

### Exercise 2.3: Multi-stage build analysis

**Stage 1 (builder)** làm gì:
- Dùng `python:3.11-slim` làm base
- Cài build tools cần để compile dependencies
- `pip install --user` cài packages vào `/root/.local`
- Stage này **KHÔNG** được deploy, chỉ dùng để build

**Stage 2 (runtime)** làm gì:
- Dùng `python:3.11-slim` sạch
- Tạo non-root user `appuser` (security)
- COPY packages từ builder (`/root/.local` thành `/home/appuser/.local`)
- COPY source code
- Chạy với `USER appuser` (không phải là root)
- Thêm `HEALTHCHECK` để Docker tự restart nếu fail

**Tại sao image nhỏ hơn?**
- Stage 2 không chứa gcc, build tools, pip cache
- Chỉ có Python runtime + compiled packages + source code
- Ước tính: Develop ~1 GB vs Production ~200-300 MB

### Exercise 2.4: Docker Compose architecture

```
          Client (port 80)
              │
              ▼
         ┌──────────┐
         │  Nginx    │  ← Reverse proxy & load balancer
         └────┬─────┘
              │
      ┌───────┼───────┐
      ▼       ▼       ▼
  ┌──────┐ ┌──────┐ ┌──────┐
  │Agent1│ │Agent2│ │AgentN│  ← FastAPI app (scalable)
  └──┬───┘ └──┬───┘ └──┬───┘
     └────────┼────────┘
              │
      ┌───────┴───────┐
      ▼               ▼
  ┌──────┐       ┌──────┐
  │Redis │       │Qdrant│  ← Cache & Vector DB
  └──────┘       └──────┘
```

4 services: Agent, Redis, Qdrant, Nginx. Tất cả trong network `internal` (bridge), chỉ Nginx expose port 80/443 ra ngoài.

---

## Part 3: Cloud Deployment

### Exercise 3.1: Railway deployment
- URL: https://minhclouddeployment-production.up.railway.app/
- Screenshots:
  - [Dashboard](https://github.com/minhphan2/lab12_PhanTuanMinh_2A202600422/blob/df75bf62ed16674ce729333437e58d8647304c22/screenshots/lab12%201.png)
  - [Service running](https://github.com/minhphan2/lab12_PhanTuanMinh_2A202600422/blob/df75bf62ed16674ce729333437e58d8647304c22/screenshots/lab12%202.png)
  - [Test results](https://github.com/minhphan2/lab12_PhanTuanMinh_2A202600422/blob/df75bf62ed16674ce729333437e58d8647304c22/screenshots/lab12%203.png)

Railway sử dụng file `railway.toml` để cấu hình:
- **Builder:** `NIXPACKS` (auto-detect Python, không cần Dockerfile)
- **Start command:** `uvicorn app:app --host 0.0.0.0 --port $PORT` (Railway inject PORT tự động)
- **Health check:** `/health` endpoint với timeout 30s
- **Restart policy:** `ON_FAILURE`, tối đa 3 lần retry
- **Env vars:** Set qua Dashboard hoặc CLI (`railway variables set KEY=VALUE`)

### Exercise 3.2: So sánh railway.toml vs render.yaml

| Feature | Railway (`railway.toml`) | Render (`render.yaml`) |
|---------|------------------------|----------------------|
| Format | TOML | YAML |
| Builder | NIXPACKS (auto) hoặc DOCKERFILE | python runtime hoặc docker |
| Region | Tự động | Chọn được (e.g., singapore) |
| Free tier | $5 credit | 750h/month |
| Env vars | CLI hoặc Dashboard | Inline trong yaml hoặc Dashboard |
| Auto deploy | Có (git push) | Có (`autoDeploy: true`) |
| Health check | `healthcheckPath` | `healthCheckPath` |
| Redis | Add-on riêng | Khai báo trong render.yaml |
| Secret management | `railway variables set` | `sync: false` hoặc `generateValue: true` |

---

## Part 4: API Security

### Exercise 4.1: API Key authentication

- API key được check ở **middleware/dependency** qua header `X-API-Key`
- Nếu sai key thì trả về HTTP **401 Unauthorized** với message "Invalid or missing API key"
- Rotate key: thay đổi env var `AGENT_API_KEY` rồi restart service, không cần sửa code

### Exercise 4.3: Rate limiting

- **Algorithm:** Sliding window (dùng `deque` lưu timestamp, xóa entry cũ hơn 60s)
- **Limit:** 10-20 requests/minute per user (cấu hình qua `RATE_LIMIT_PER_MINUTE`)
- **Bypass cho admin:** Có thể thêm whitelist API key hoặc tăng limit cho role admin
- Khi vượt limit thì HTTP **429 Too Many Requests** với header `Retry-After: 60`

### Exercise 4.4: Cost guard implementation

Approach: Track spending theo user/day, so sánh với budget limit.

```python
import redis
from datetime import datetime

r = redis.Redis()

def check_budget(user_id: str, estimated_cost: float) -> bool:
    month_key = datetime.now().strftime("%Y-%m")
    key = f"budget:{user_id}:{month_key}"
    
    current = float(r.get(key) or 0)
    if current + estimated_cost > 10:  # $10/month limit
        return False
    
    r.incrbyfloat(key, estimated_cost)
    r.expire(key, 32 * 24 * 3600)  # TTL 32 days
    return True
```

Logic: Mỗi user có budget $10/tháng, track trong Redis với key `budget:{user_id}:{year-month}`, tự expire sau 32 ngày.

---

## Part 5: Scaling & Reliability

### Exercise 5.1: Health checks

```python
@app.get("/health")
def health():
    """Liveness probe — container còn sống không?"""
    return {
        "status": "ok",
        "uptime_seconds": round(time.time() - START_TIME, 1),
        "version": "1.0.0",
        "timestamp": datetime.now(timezone.utc).isoformat(),
    }

@app.get("/ready")
def ready():
    """Readiness probe — sẵn sàng nhận traffic không?"""
    if not _is_ready:
        raise HTTPException(503, "Agent not ready")
    return {"ready": True}
```

- `/health` (Liveness): Platform gọi định kỳ, non-200 → restart container
- `/ready` (Readiness): Load balancer dùng để route traffic, 503 khi đang startup/shutdown

### Exercise 5.2: Graceful shutdown

```python
def handle_sigterm(signum, frame):
    logger.info(f"Received signal {signum}")
    # uvicorn tự handle graceful shutdown qua lifespan

signal.signal(signal.SIGTERM, handle_sigterm)
```

Khi nhận SIGTERM: ngừng nhận request mới → chờ request đang xử lý hoàn thành (timeout 30s) → đóng connections → exit.

### Exercise 5.3: Stateless design

**Anti-pattern (Stateful):**
```python
conversation_history = {}  # ❌ Mất khi restart, không share giữa instances
```

**Correct (Stateless):**
```python
# ✅ Lưu trong Redis — bất kỳ instance nào cũng đọc được
def save_session(session_id, data):
    redis.setex(f"session:{session_id}", 3600, json.dumps(data))
```

Tại sao: Khi scale ra 3 instances, mỗi instance có memory riêng. User gửi request 1 → Instance A (lưu memory), request 2 → Instance B (không có data!). Redis giải quyết bằng cách lưu state ở nơi chung.

### Exercise 5.4-5.5: Load balancing & Stateless test

- `docker compose up --scale agent=3` tạo 3 agent instances
- Nginx phân tán requests theo round-robin
- Nếu 1 instance die → traffic tự chuyển sang instances còn lại
- Test stateless: tạo conversation → kill instance → conversation vẫn còn trong Redis

