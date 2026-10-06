FROM golang:1.24 AS build

WORKDIR /app

COPY go.mod go.sum ./
RUN go mod download

COPY . .

RUN go build -o main main.go

FROM amazonlinux:2023

WORKDIR /app

COPY --from=build /app/main .

EXPOSE 8080

ENTRYPOINT ["./main"]
