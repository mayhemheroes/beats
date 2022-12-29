FROM fuzzers/go-fuzz:1.2.0 as builder

ARG project='beats'
ARG fuzzTargetPath='x-pack/filebeat/processors/decode_cef/cef/fuzz'
ARG fuzzTarget='decode-cef-fuzz'

ADD . ${project}
WORKDIR ${project}
RUN GO_ENABLED=0 go-fuzz-build -libfuzzer -o /${fuzzTarget}.a ./${fuzzTargetPath}
RUN clang -fsanitize=fuzzer /${fuzzTarget}.a -o /${fuzzTarget}.libfuzzer

FROM ubuntu:20.04
COPY --from=builder /decode-cef-fuzz.libfuzzer /
