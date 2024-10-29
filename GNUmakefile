ifeq ($(GOPATH),)
	GOPATH:=$(shell go env GOPATH)
endif

.EXPORT_ALL_VARIABLES:
	GO_VERSION=$(GO_VERSION)
	ARCH=$(ARCH)

default: build

tools:
	@echo "==> Installing go <=="
	@sh -c "$(CURDIR)/scripts/tools.sh $(GO_VERSION) $(ARCH)"

fmt:
	@echo "==> Formatting code <=="
	find . -name '*.go' | grep -v vendor | xargs gofmt -s -w

fmtcheck:
	@sh -c "'$(CURDIR)/scripts/fmtcheck.sh'"

build:
	goreleaser release --snapshot --clean

test:
	@echo "==> Testing <=="
	cd cmd/ && go test -v -tags=all

testworkspaces:
	@echo "==> Testing Workspace <=="
	cd cmd/ && go test -v -tags=workspace
