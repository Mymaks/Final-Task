FROM golang:1.19-alpine

WORKDIR /app

COPY go.mod ./

COPY *.go ./

RUN go build -o /docker-server-go

EXPOSE 8080

CMD [ "/docker-server-go" ]