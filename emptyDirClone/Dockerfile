# syntax=docker/dockerfile:1
ARG GO_VERSION=1.21
FROM --platform=$BUILDPLATFORM golang:${GO_VERSION} as builder

WORKDIR /src

RUN --mount=type=cache,target=/go/pkg/mod/ \
    --mount=type=bind,source=go.sum,target=go.sum \
    --mount=type=bind,source=go.mod,target=go.mod \
    go mod download -x

ARG TARGETOS
ARG TARGETARCH

RUN --mount=type=bind,target=. \
    --mount=type=cache,target=/go/pkg/mod \
    --mount=type=cache,target=/root/.cache/go-build \
    GOOS=$TARGETOS GOARCH=$TARGETARCH \
    go build -ldflags "-s -w -X main.version=$(git rev-parse HEAD)" -o /emptydirclone main.go

FROM alpine
# Add util-linux to get a new version of losetup.
RUN apk update && apk upgrade && apk add util-linux coreutils
COPY --from=builder /emptydirclone /emptydirclone
ENTRYPOINT ["/emptydirclone"]
