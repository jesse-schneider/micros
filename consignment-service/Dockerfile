# consignment-service/Dockerfile

# We use the offical golang image, which contains all the
# correct build tools and libraries. Notice `as builder`,
# this gives this container a name that we can reference later on.
FROM golang:1.9.0 as builder

# set our workdir to our current service in the GOPATH
WORKDIR /go/src/consignment-service

# copy the current code into our workdir
COPY . .

# here we're pulling in godep, which is a dependency manager tool,
# we're going to use dep instead of go get, to get around a few quirks
# in how go get works with sub-packages.
RUN go get -u github.com/golang/dep/cmd/dep

# create a dep project, and run 'ensure', which will pull in all
# of the dependencies within this directory.
RUN dep init && dep ensure

# build the binary, with a few flags which will allow
# us to run this binary in Alpine/debian
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo .

# Here we're using a second FROM statement, which is strange,
# but this tells docker to start a new build process with this image
FROM debian:latest

# security related package, good to have.
RUN apk --no-cache add ca-certificates

#same as before, create directory for app
RUN mkdir /app
WORKDIR /app

# here, instead of copying the binary from our host machine,
#we pull the binary from hte container named `builder`, within,
# this build context. THis reaches into our previous image, finds
# the binary we built, and pulls in into this container. Amazing!
COPY --from=builder /go/src/consignment-service/consignment-service .

# run the binary as per usual! This time with a binary build in a
#separate container, with all of the correct dependencies and
# run time libraries.
CMD ["./consignment-service"]
