FROM python:3.11-slim

WORKDIR /app

ARG ARTIFACT_NAME

COPY artifacts/${ARTIFACT_NAME} /tmp/app.tar.gz

RUN tar -xzf /tmp/app.tar.gz -C /app && rm /tmp/app.tar.gz

ENV PYTHONPATH=/app/app_package

EXPOSE 8000

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]