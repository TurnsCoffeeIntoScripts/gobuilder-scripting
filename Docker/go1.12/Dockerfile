FROM golang:1.12 AS builder

# Arguments for the labels
ARG MAINTAINER
ARG AUTHORS
ARG VERSION
ARG DESCRIPTION

# Arguments regarding structure/fs
ARG GO_MOD_REL_LOCATION

LABEL maintainer=${MAINTAINER}
LABEL authors=${AUTHORS}
LABEL version=${VERSION}
LABEL description=${DESCRIPTION}

# Copy everything from the jira-api-ressource module to /app in the image
COPY . /app

# Set the working directory to /app where our code and scripts are
WORKDIR /app

# Environment variables
ENV CGO_ENABLED 0
ENV GOOS linux
ENV GOARCH amd64

# Set the go module file location
ENV GOMOD ${GO_MOD_REL_LOCATION}

# Launch the make tool on the default target
RUN make

FROM alpine:edge AS resource

RUN apk --no-cache add \
        curl \
        jq \
        bash \
        ca-certificates \
        && update-ca-certificates 2>/dev/null || true \
;

# Copy the built binary into the bin folder
COPY --from=builder /app/bin/jiraApiIssueResource /usr/local/bin/

# Copy assets
COPY assets/check /opt/resource/check
COPY assets/in /opt/resource/in
COPY assets/out /opt/resource/out

RUN chmod +x /opt/resource/check /opt/resource/in /opt/resource/out

WORKDIR /opt/resource

FROM resource
