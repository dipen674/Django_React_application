# Stage 1: Builder

FROM python:3.9-slim AS builder
ENV PYTHONDONTWRITEBYTECODE 1
ENV PYTHONUNBUFFERED 1
WORKDIR /app
RUN apt-get update && apt-get install -y \
	gcc \
	libpq-dev \
	&& rm -rf /var/lib/apt/lists/*
COPY requirements.txt .
RUN pip install --upgrade pip
RUN pip install -r requirements.txt

# Stage 2: Production (Runtime)

FROM python:3.9-slim AS production 
WORKDIR /app

# 🎯 FIX 2: Install runtime dependency and clean up cache in the same layer
RUN apt-get update && apt-get install -y \
    libpq5 \
    && rm -rf /var/lib/apt/lists/*

COPY --from=builder /usr/local/lib/python3.9/site-packages /usr/local/lib/python3.9/site-packages
COPY --from=builder /usr/local/bin/gunicorn /usr/local/bin/
COPY . .

EXPOSE 8000
CMD ["sh", "-c", "sleep 5 && python manage.py migrate --no-input && gunicorn mysite.wsgi:application --bind 0.0.0.0:8000"]