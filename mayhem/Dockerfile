FROM golang:1.18 AS builder

RUN apt-get update && apt-get install -y clang && rm -rf /var/lib/apt/lists/*

# Install go-fuzz-build at a commit that supports go:embed (July 2022) and is
# compatible with Go 1.18. The @latest version requires golang.org/x/tools >= v0.44.0
# which needs Go 1.25+.
RUN mkdir /tmp/igf && cd /tmp/igf && go mod init igf && printf 'module igf\n\ngo 1.18\n\nrequire github.com/dvyukov/go-fuzz v0.0.0-20220726122315-1d375ef9f9f6\n\nrequire golang.org/x/tools v0.1.12\n' > go.mod && GOFLAGS=-mod=mod go get golang.org/x/tools@v0.1.12 && GOFLAGS=-mod=mod go install github.com/dvyukov/go-fuzz/go-fuzz-build && rm -rf /tmp/igf

ARG project='beats'
ARG fuzzTargetPath='x-pack/filebeat/processors/decode_cef/cef/fuzz'
ARG fuzzTarget='decode-cef-fuzz'

ADD . /${project}
WORKDIR /${project}
# Add go-fuzz-dep to the module graph (required by go-fuzz-build at instrumentation time)
RUN GOFLAGS=-mod=mod go get github.com/dvyukov/go-fuzz@v0.0.0-20220726122315-1d375ef9f9f6
RUN go-fuzz-build -libfuzzer -o /${fuzzTarget}.a ./${fuzzTargetPath}
RUN clang -fsanitize=fuzzer /${fuzzTarget}.a -o /${fuzzTarget}.libfuzzer

FROM ubuntu:20.04
COPY --from=builder /decode-cef-fuzz.libfuzzer /
