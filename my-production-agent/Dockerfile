# ============================================================
# ADVANCED Dockerfile — Multi-stage build
# ============================================================
FROM python:3.11-slim AS builder
WORKDIR /app

RUN apt-get update && apt-get install -y \
    gcc \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir --user -r requirements.txt


FROM python:3.11-slim AS runtime
RUN groupadd -r appuser && useradd -r -g appuser appuser
WORKDIR /app

COPY --from=builder /root/.local /home/appuser/.local

COPY app/ app/

RUN chown -R appuser:appuser /app
USER appuser

ENV PATH=/home/appuser/.local/bin:$PATH
ENV PYTHONPATH=/app

EXPOSE 8000

HEALTHCHECK --interval=30s --timeout=10s --start-period=5s --retries=3 \
    CMD python -c "import urllib.request, os; port=os.environ.get('PORT', '8000'); urllib.request.urlopen(f'http://localhost:{port}/health')" || exit 1

CMD uvicorn app.main:app --host 0.0.0.0 --port ${PORT:-8000} --workers 2
