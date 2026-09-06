FROM python:3.10-slim-bookworm

ENV FLASK_RUN_HOST=0.0.0.0

WORKDIR /app

# Install system dependencies required to build uWSGI
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    build-essential \
    gcc \
    libpcre3-dev \
    libffi-dev \
    libssl-dev \
    openssl \
    && rm -rf /var/lib/apt/lists/*

# Install Pipenv
RUN pip install --no-cache-dir pipenv

# Copy dependency files first
COPY Pipfile Pipfile.lock ./

# Install Python dependencies from Pipfile.lock
RUN pipenv install --system --deploy

# Copy application code
COPY . .

# Create runtime directory for uWSGI socket
RUN mkdir -p /var/www/app

# Start uWSGI
CMD ["uwsgi", "--http", ":5000", "--master", "--enable-threads", "-s", "/var/www/app/app.sock", "--manage-script-name", "--mount", "/=app:app"]