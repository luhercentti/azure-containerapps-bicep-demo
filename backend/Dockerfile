FROM python:3.11-slim

WORKDIR /app

# Upgrade OS packages to pick up security patches (e.g. glibc CVE-2026-0861)
RUN apt-get update && apt-get upgrade -y && rm -rf /var/lib/apt/lists/*

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt \
    # Upgrade build tools to patched versions then remove them — not needed at runtime
    && pip install --upgrade wheel setuptools \
    && pip uninstall -y wheel setuptools

COPY . .

EXPOSE 8000

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]