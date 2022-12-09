FROM golang:1.19-alpine AS builder

RUN apk add --no-cache git build-base
WORKDIR /tmp/pocketbase

COPY go.mod go.sum .
RUN go mod download
COPY pocketbase.go .
COPY src src

RUN go build -o ./out/pocketbase .

FROM alpine:latest

RUN apk update && apk add ca-certificates && rm -rf /var/cache/apk/*
COPY --from=builder /tmp/pocketbase/out/pocketbase /app/pocketbase

EXPOSE 8090
CMD ["/app/pocketbase", "serve", "--http=0.0.0.0:8090"]
