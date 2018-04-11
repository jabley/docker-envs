FROM golang:alpine as compiler
COPY echo.go /
RUN go build -o /echo /echo.go 

# Runtime image
FROM scratch
ENV PROTOCOL="https"
ENV FOO_OPTS "protocol=${PROTOCOL}"
COPY --from=compiler /echo /
CMD ["./echo"]
