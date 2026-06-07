FROM golang:1.19-alpine AS builder

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .
RUN go build -o myapp ./cmd/api/

FROM alpine:3.18

RUN adduser -D -u 1001 appuser \
    && apk add --no-cache wget

WORKDIR /app
COPY --from=builder /app/myapp .
RUN chown appuser:appuser ./myapp

USER appuser
EXPOSE 8080

HEALTHCHECK --interval=30s --timeout=5s --start-period=10s --retries=3 \
    CMD wget -qO- http://localhost:8080/health || exit 1

CMD ["./myapp"]
