# multistage building as described in https://docs.docker.com/guides/golang/build-images/
FROM golang:latest AS building-backend-app

WORKDIR /backend

# .dockerignore excludes unnecessary files
COPY . .

# CGO_ENABLED=0 for static linking to not depend on glibc presence on deploying-backend-app image
RUN CGO_ENABLED=0 go build -o backend-app ./cmd/api/ && go clean -cache -modcache

# deploy the application binary into a lean image
FROM alpine:latest AS deploying-backend-app

ARG port=8081
# we need it as ARG will not survive by the time it is used in HEALTHCHECK
ENV PORT=$port

# curl will be used in HEALTHCHECK
RUN apk add --no-cache curl

WORKDIR /backend

COPY --from=building-backend-app /backend/backend-app ./backend-app

# creating non-root user and changing owner
# commented-out for debian/ubuntu
# RUN groupadd -r appuser && useradd -r -g appuser appuser#
# left for alpine
RUN addgroup -S appuser && adduser -S appuser -G appuser
RUN chown -R appuser:appuser .

# switching to a non-root user
USER appuser

EXPOSE ${PORT}

# without -f flag curl will return 0 even if HTTP status code is 4xx/5xx
HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
  CMD curl -f http://localhost:${PORT}/health

CMD ["./backend-app"]