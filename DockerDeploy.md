version: "3"

services:
  db:
    container_name: bishasto_db
    image: postgres:12.3
    restart: always
    volumes:
      - postgres_data:/var/lib/postgresql/data
    env_file: ./.env.docker

  web:
    container_name: bishasto
    build: .
    restart: always
    command: gunicorn bishasto.wsgi:application --bind 0.0.0.0:8000
    expose:
      - 8000
    volumes:
      - media_data:/app/media
      - static_data:/app/static
    env_file:
      - .env
    depends_on:
      - db
    links:
      - db:db

  nginx:
    container_name: bishasto_nginx
    build: ./nginx
    restart: always
    ports:
      - 8086:80
    depends_on:
      - web
    volumes:
    - media_data:/media
    - static_data:/static

volumes:
  media_data:
  static_data:
  postgres_data:
 