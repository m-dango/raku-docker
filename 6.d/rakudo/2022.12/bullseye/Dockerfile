FROM buildpack-deps:bullseye AS builder

ARG rakudo_version=2022.12

RUN wget -O /tmp/rakudo-${rakudo_version}.tar.gz "https://rakudo.org/dl/rakudo/rakudo-${rakudo_version}.tar.gz"

WORKDIR /usr/src/raku
RUN tar -xzf /tmp/rakudo-${rakudo_version}.tar.gz -C /usr/src/raku --strip-components=1
RUN perl Configure.pl --backends=moar --gen-moar --gen-nqp --prefix="/raku"
RUN make install

FROM debian:bullseye
WORKDIR /root
COPY --from=builder /raku /raku
ENV PATH /raku/bin:/raku/share/perl6/site/bin:$PATH

CMD ["raku"]
