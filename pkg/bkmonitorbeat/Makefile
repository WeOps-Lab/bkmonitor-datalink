RELEASE_PATH ?= build
BUILD_NO ?= 0
NAME = bkmonitorbeat
VERSION = $(shell git describe --tags --always)
COMMIT_ID = $(shell git rev-parse HEAD)

.PHONY: lint
lint:
	gofumpt -w .
	goimports-reviser -project-name "github.com/TencentBlueKing/bkmonitor-datalink/pkg" ./...

.PHONY: build
build:
	env VERSION=$(VERSION) RELEASE_PATH=$(RELEASE_PATH) ./script/gse_package.sh

.PHONY: test
test:
	go test ./...

beat:
	CGO_ENABLED=0 GO111MODULE=on GOOS=linux GOARCH=amd64 go build -tags 'basetask basescheduler bkmonitorbeat' -ldflags=" -X main.BeatName=${NAME} -X main.Version=${VERSION}" -o ./build/linux/x86_64/bkmonitorbeat main.go
	CGO_ENABLED=0 GO111MODULE=on GOOS=linux GOARCH=arm64 go build -tags 'basetask basescheduler bkmonitorbeat' -ldflags=" -X main.BeatName=${NAME} -X main.Version=${VERSION}" -o ./build/linux/arm64/bkmonitorbeat main.go
	CGO_ENABLED=0 GO111MODULE=on GOOS=windows GOARCH=amd64 go build -tags 'basetask basescheduler bkmonitorbeat' -ldflags=" -X main.BeatName=${NAME} -X main.Version=${VERSION}" -o ./build/windows/bkmonitorbeat.exe main.go
	tar -czf ./build/bkmonitorbeat_$(VERSION).tar.gz -C ./build linux windows