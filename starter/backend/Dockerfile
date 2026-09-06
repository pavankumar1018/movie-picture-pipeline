FROM public.ecr.aws/docker/library/python:3.10-alpine

ENV FLASK_RUN_HOST=0.0.0.0

WORKDIR /app

# Install system dependencies required to build uWSGI
RUN apk add --no-cache \
    build-base \
    linux-headers \
    pcre \
    pcre-dev \
    openssl \
    libffi-dev \
    && pip install --no-cache-dir pipenv

# Create application user
RUN adduser -u 1000 -D app

# Copy dependency files first
COPY Pipfile Pipfile.lock /app/

# Install Python dependencies
RUN pipenv install --system --deploy

# Copy application code
COPY . /app

# Create uWSGI socket directory
RUN mkdir -p /var/www/app \
    && chown -R app:app /app /var/www/app

# Run as non-root user
USER app

EXPOSE 5000

CMD ["uwsgi", "--http", ":5000", "--master", "--enable-threads", "-s", "/var/www/app/app.sock", "--manage-script-name", "--mount", "/=app:app"]