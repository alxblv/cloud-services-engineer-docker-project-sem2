FROM golang:latest

WORKDIR /backend

# .dockerignore excludes unnecessary files
COPY . .

RUN go build -o backend ./cmd/api/

EXPOSE 8081

CMD ["./backend"]