FROM python:3.6-slim

WORKDIR /app

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

# Pins pip and setup tools to versions that still support python 3.6 due to upstream constraints. no-cache-dir forces a new download to avoid stale caches or broken downloads
# Ensures every build is reproducible but creates slower builds
RUN pip install --no-cache-dir "pip<22" "setuptools<60" wheel
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 8082

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]