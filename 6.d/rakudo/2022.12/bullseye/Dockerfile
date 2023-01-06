FROM buildpack-deps:bullseye AS builder

ARG rakudo_version=2022.12
ARG gpg_key="59E634736AFDCF9C6DBAC382602D51EACA887C01"

RUN wget -O /tmp/rakudo.tar.gz "https://rakudo.org/dl/rakudo/rakudo-${rakudo_version}.tar.gz"
RUN wget -O /tmp/rakudo.tar.gz.asc "https://rakudo.org/dl/rakudo/rakudo-${rakudo_version}.tar.gz.asc"
RUN wget -O /tmp/key.asc "https://rakudo.org/keys/justin_devuyst-${gpg_key}.asc"

ENV GNUPGHOME /tmp/gnupg
RUN mkdir "$GNUPGHOME"
RUN gpg --batch --import /tmp/key.asc
RUN gpg --batch --export "$gpg_key" > /tmp/${gpg_key}.asc
RUN rm -r "$GNUPGHOME"
RUN mkdir "$GNUPGHOME"
RUN gpg --batch --import /tmp/${gpg_key}.asc
RUN gpg --batch --verify /tmp/rakudo.tar.gz.asc /tmp/rakudo.tar.gz

WORKDIR /usr/src/raku
RUN tar -xzf /tmp/rakudo.tar.gz -C /usr/src/raku --strip-components=1
RUN perl Configure.pl --backends=moar --gen-moar --gen-nqp --prefix="/raku"
RUN make install

FROM debian:bullseye
WORKDIR /root
COPY --from=builder /raku /raku
ENV PATH /raku/bin:/raku/share/perl6/site/bin:$PATH

CMD ["raku"]
