FROM python:3.11-slim

WORKDIR /app

ARG ARTIFACT_NAME

COPY artifacts/ /tmp/artifacts/

RUN tar -xzf /tmp/artifacts/${ARTIFACT_NAME} -C /app && rm -rf /tmp/artifacts

ENV PYTHONPATH=/app/app_package

EXPOSE 8000

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]