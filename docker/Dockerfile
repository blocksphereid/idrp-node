# ===== FIRTS STAGE ======
FROM paritytech/ci-linux:production as builder
# This is the build stage for uniqueone. Here we create the binary.
ARG PROFILE=release
ARG VCS_REF
ARG VERSION
ARG BUILD_DATE

ENV RUST_BACKTRACE 1

LABEL id.blocksphere.uniqueone.node.image.authors="devteam@blocksphere.id" \
	id.blocksphere.uniqueone.node.image.vendor="Blocksphere" \
	id.blocksphere.uniqueone.node.image.title="blocksphereid/uniqueone-node" \
	id.blocksphere.uniqueone.node.image.description="uniqueone: a platform for web3" \
	id.blocksphere.uniqueone.node.image.source="https://github.com/blocksphereid/uniqueone-node/blob/${VCS_REF}/docker/Dockerfile" \
	id.blocksphere.uniqueone.node.image.revision="${VCS_REF}" \
	id.blocksphere.uniqueone.node.image.version="${VERSION}" \
	id.blocksphere.uniqueone.node.image.created="${BUILD_DATE}" \
	id.blocksphere.uniqueone.node.image.documentation="https://github.com/blocksphereid/uniqueone-node/tree/${VCS_REF}"
	
WORKDIR /uniqueone

COPY ./node /uniqueone/node
COPY ./runtime /uniqueone/runtime
COPY ./Cargo.lock /uniqueone/Cargo.lock
COPY ./Cargo.toml /uniqueone/Cargo.toml

RUN cargo build --$PROFILE

# ===== SECOND STAGE ======
FROM debian:buster-slim
# This is the 2nd stage: a very small image where we copy the Polkadot binary.
ARG PROFILE=release
ARG VCS_REF
ARG VERSION
ARG BUILD_DATE

ENV RUST_BACKTRACE 1

LABEL id.blocksphere.uniqueone.node.image.authors="devteam@blocksphere.id" \
	id.blocksphere.uniqueone.node.image.vendor="Blocksphere" \
	id.blocksphere.uniqueone.node.image.title="blocksphereid/uniqueone-node" \
	id.blocksphere.uniqueone.node.image.description="uniqueone: a platform for web3" \
	id.blocksphere.uniqueone.node.image.source="https://github.com/blocksphereid/uniqueone-node/blob/${VCS_REF}/docker/Dockerfile" \
	id.blocksphere.uniqueone.node.image.revision="${VCS_REF}" \
	id.blocksphere.uniqueone.node.image.version="${VERSION}" \
	id.blocksphere.uniqueone.node.image.created="${BUILD_DATE}" \
	id.blocksphere.uniqueone.node.image.documentation="https://github.com/blocksphereid/uniqueone-node/tree/${VCS_REF}"

# show backtraces
ENV RUST_BACKTRACE 1

COPY --from=builder /uniqueone/target/$PROFILE/uniqueone /usr/local/bin

RUN apt-get update && \
	DEBIAN_FRONTEND=noninteractive apt-get upgrade -y && \
	DEBIAN_FRONTEND=noninteractive apt-get install -y curl && \
	apt-get autoremove -y && \
	apt-get clean && \
	find /var/lib/apt/lists/ -type f -not -name lock -delete; \
	useradd -m -u 1000 -U -s /bin/sh -d /uniqueone uniqueone && \
	mkdir -p /uniqueone/.local/share && \
	mkdir /data && \
	chown -R uniqueone:uniqueone /data && \
	ln -s /data /uniqueone/.local/share/uniqueone

USER uniqueone

# check if executable works in this container
RUN /usr/local/bin/uniqueone --version

EXPOSE 30333 9933 9944 30334 9934 9945
VOLUME ["/data"]

ENTRYPOINT ["/usr/local/bin/uniqueone"]