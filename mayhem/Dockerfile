# Build Geth in a stock Go builder container
FROM fuzzers/go-fuzz:1.2.0 as builder

ADD . beats
WORKDIR /go/beats

RUN GO_ENABLED=0 go-fuzz-build -libfuzzer -o /fuzz.a ./x-pack/filebeat/processors/decode_cef/cef/fuzz/
RUN clang -fsanitize=fuzzer /fuzz.a -o /fuzz.libfuzzer

FROM ubuntu:20.04
COPY --from=builder /fuzz.libfuzzer /

# CMD ["./bitutil.libfuzzer"]