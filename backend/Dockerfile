# multistage building as described in https://docs.docker.com/guides/golang/build-images/
FROM golang:latest AS building-backend-app

WORKDIR /backend

# .dockerignore excludes unnecessary files
COPY . .

# CGO_ENABLED=0 for static linking to not depend on glibc presence on deploying-backend-app image
RUN CGO_ENABLED=0 go build -o backend-app ./cmd/api/ && go clean -cache -modcache

# deploy the application binary into a lean image
FROM alpine:latest AS deploying-backend-app

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

EXPOSE 8081

CMD ["./backend-app"]