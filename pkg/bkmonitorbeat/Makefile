RELEASE_PATH ?= build
BUILD_NO ?= 0
NAME = bkmonitorbeat
VERSION ?= 3.29.1659-weops.1.0.5
COMMIT_ID = $(shell git rev-parse HEAD)
GO ?= go
GO_BUILD_TAGS ?= basetask basescheduler bkmonitorbeat
GO_LDFLAGS ?= -X main.BeatName=$(NAME) -X main.Version=$(VERSION)
LINUX_PACKAGE_NAME ?= $(NAME)_linux_$(VERSION).tar.gz
WINDOWS_PACKAGE_NAME ?= $(NAME)_windows_$(VERSION).tar.gz
ALL_PACKAGE_NAME ?= $(NAME)_$(VERSION).tar.gz

define build_beat
	CGO_ENABLED=0 GO111MODULE=on GOOS=$(1) GOARCH=$(2) $(GO) build -tags '$(GO_BUILD_TAGS)' -ldflags="$(GO_LDFLAGS)" -o $(3) main.go
endef

.PHONY: lint
lint:
	gofumpt -w .
	goimports-reviser -project-name "github.com/TencentBlueKing/bkmonitor-datalink/pkg" ./...

.PHONY: build
build:
	env VERSION=$(VERSION) RELEASE_PATH=$(RELEASE_PATH) ./script/gse_package.sh

.PHONY: test
test:
	$(GO) test ./...

.PHONY: beat beat-linux beat-windows package-linux package-windows package-all
beat:
	$(MAKE) beat-linux
	$(MAKE) beat-windows
	$(MAKE) package-all

beat-linux:
	mkdir -p ./$(RELEASE_PATH)/linux/x86_64 ./$(RELEASE_PATH)/linux/arm64
	$(call build_beat,linux,amd64,./$(RELEASE_PATH)/linux/x86_64/$(NAME))
	$(call build_beat,linux,arm64,./$(RELEASE_PATH)/linux/arm64/$(NAME))
	$(MAKE) package-linux

beat-windows:
	mkdir -p ./$(RELEASE_PATH)/windows
	$(call build_beat,windows,amd64,./$(RELEASE_PATH)/windows/$(NAME).exe)
	$(MAKE) package-windows

package-linux:
	tar -czf ./$(RELEASE_PATH)/$(LINUX_PACKAGE_NAME) -C ./$(RELEASE_PATH) linux

package-windows:
	tar -czf ./$(RELEASE_PATH)/$(WINDOWS_PACKAGE_NAME) -C ./$(RELEASE_PATH) windows

package-all:
	tar -czf ./$(RELEASE_PATH)/$(ALL_PACKAGE_NAME) -C ./$(RELEASE_PATH) linux windows
