# Base image
FROM python:3.9-slim

WORKDIR /app

COPY requirements.txt .

RUN pip install --no-cache-dir -r requirements.txt

COPY . .

EXPOSE 5000

# ENTRYPOINT = fixed command
ENTRYPOINT ["python"]

# CMD = default argument (can override)
CMD ["app.py"]
