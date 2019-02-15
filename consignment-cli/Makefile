build:
	GOOS=linux GOARCH=amd64 go build
	docker build -t consignment-cli .
run:
	docker run -e MICRO_REGISTY=mdns consignment-cli
