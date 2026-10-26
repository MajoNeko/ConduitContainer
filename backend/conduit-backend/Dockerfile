# ---- Build stage ----
FROM python:3.6-slim AS build

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# Pins pip and setup tools to versions that still support python 3.6 due to upstream constraints. no-cache-dir forces a new download to avoid stale caches or broken downloads
# Ensures every build is reproducible but creates slower builds
RUN pip install --no-cache-dir "pip<22" "setuptools<60" wheel

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# ---- Runtime stage ----
FROM python:3.6-slim

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

COPY --from=build /usr/local/lib/python3.6/site-packages /usr/local/lib/python3.6/site-packages
COPY --from=build /usr/local/bin /usr/local/bin

COPY . .

EXPOSE 8000

CMD ["gunicorn", "conduit.wsgi:application", "--bind", "0.0.0.0:8000", "--workers", "2"]