services:
  postgres:
    image: postgres:14-alpine
    ports:
      - 5432:5432
    volumes:
      - ./data:/var/lib/postgresql/data
      - ./tmp:/tmp
    environment:
      - POSTGRES_PASSWORD=password
#      - POSTGRES_USER=postgres
#      - POSTGRES_DB=postgres
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "postgres"]
      
  adminer:
    image: adminer
    restart: always
    ports:
      - 8080:8080
