FROM python:3.11-slim

RUN mkdir /app

WORKDIR /app

# Prevents Python from writing pyc files to disk
ENV PYTHONDONTWRITEBYTECODE=1
#Prevents Python from buffering stdout and stderr
ENV PYTHONUNBUFFERED=1 
RUN apt-get update && apt-get install -y libpq-dev gcc
RUN pip install --upgrade pip 

COPY pyproject.toml .
COPY . .
RUN pip install .
RUN pip install psycopg2-binary

EXPOSE 8000

CMD ["gunicorn", "book_shop.wsgi:application", "--bind", "0.0.0.0:8000"]