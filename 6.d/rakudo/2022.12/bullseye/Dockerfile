FROM buildpack-deps:bullseye AS builder

ARG rakudo_version=2022.12
ARG zef_version=0.14.6
ARG linenoise_version=0.1.2
ARG gpg_key="59E634736AFDCF9C6DBAC382602D51EACA887C01"
ENV PATH /raku/bin:/raku/share/perl6/site/bin:$PATH

RUN wget -O /tmp/rakudo.tar.gz "https://rakudo.org/dl/rakudo/rakudo-${rakudo_version}.tar.gz"
RUN wget -O /tmp/rakudo.tar.gz.asc "https://rakudo.org/dl/rakudo/rakudo-${rakudo_version}.tar.gz.asc"

RUN gpg --batch --keyserver hkps://keys.openpgp.org --recv-keys "$gpg_key"
RUN gpg --batch --verify /tmp/rakudo.tar.gz.asc /tmp/rakudo.tar.gz

WORKDIR /usr/src/raku
RUN tar -xzf /tmp/rakudo.tar.gz -C . --strip-components=1
RUN perl Configure.pl --backends=moar --gen-moar --gen-nqp --prefix="/raku"
RUN make install

WORKDIR /usr/src/zef
RUN wget -O /tmp/zef.tar.gz "https://github.com/ugexe/zef/archive/refs/tags/v${zef_version}.tar.gz"
RUN tar -xzf /tmp/zef.tar.gz -C . --strip-components=1
RUN raku -I. bin/zef install .
RUN zef install "https://github.com/raku-community-modules/Linenoise/archive/refs/tags/${linenoise_version}.tar.gz"

FROM debian:bullseye
WORKDIR /root
COPY --from=builder /raku /raku
ENV PATH /raku/bin:/raku/share/perl6/site/bin:$PATH

CMD ["raku"]
