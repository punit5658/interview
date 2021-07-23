# Run the binary program produced by `go install`
FROM golang:1.7-alpine

RUN mkdir -p /app
        
WORKDIR /app

ADD . /app

CMD ["go","run","hello-world.go"]
