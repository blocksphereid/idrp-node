# ===== FIRTS STAGE ======
FROM paritytech/ci-linux:production as builder
# This is the build stage for IDRP. Here we create the binary.
ARG PROFILE=release
ARG VCS_REF
ARG VERSION
ARG BUILD_DATE

ENV RUST_BACKTRACE 1

LABEL id.blocksphere.idrp.node.image.authors="devteam@blocksphere.id" \
	id.blocksphere.idrp.node.image.vendor="Blocksphere" \
	id.blocksphere.idrp.node.image.title="blocksphereid/idrp-node" \
	id.blocksphere.idrp.node.image.description="idrp: a platform for web3" \
	id.blocksphere.idrp.node.image.source="https://github.com/blocksphereid/idrp-node/blob/${VCS_REF}/docker/Dockerfile" \
	id.blocksphere.idrp.node.image.revision="${VCS_REF}" \
	id.blocksphere.idrp.node.image.version="${VERSION}" \
	id.blocksphere.idrp.node.image.created="${BUILD_DATE}" \
	id.blocksphere.idrp.node.image.documentation="https://github.com/blocksphereid/idrp-node/tree/${VCS_REF}"
	
WORKDIR /idrp

COPY ./node /idrp/node
COPY ./runtime /idrp/runtime
COPY ./Cargo.lock /idrp/Cargo.lock
COPY ./Cargo.toml /idrp/Cargo.toml

RUN cargo build --$PROFILE

# ===== SECOND STAGE ======
FROM debian:buster-slim
# This is the 2nd stage: a very small image where we copy the Polkadot binary.
ARG PROFILE=release
ARG VCS_REF
ARG VERSION
ARG BUILD_DATE

ENV RUST_BACKTRACE 1

LABEL id.blocksphere.idrp.node.image.authors="devteam@blocksphere.id" \
	id.blocksphere.idrp.node.image.vendor="Blocksphere" \
	id.blocksphere.idrp.node.image.title="blocksphereid/idrp-node" \
	id.blocksphere.idrp.node.image.description="idrp: a platform for web3" \
	id.blocksphere.idrp.node.image.source="https://github.com/blocksphereid/idrp-node/blob/${VCS_REF}/docker/Dockerfile" \
	id.blocksphere.idrp.node.image.revision="${VCS_REF}" \
	id.blocksphere.idrp.node.image.version="${VERSION}" \
	id.blocksphere.idrp.node.image.created="${BUILD_DATE}" \
	id.blocksphere.idrp.node.image.documentation="https://github.com/blocksphereid/idrp-node/tree/${VCS_REF}"

# show backtraces
ENV RUST_BACKTRACE 1

COPY --from=builder /idrp/target/$PROFILE/idrp /usr/local/bin

RUN apt-get update && \
	DEBIAN_FRONTEND=noninteractive apt-get upgrade -y && \
	DEBIAN_FRONTEND=noninteractive apt-get install -y curl && \
	apt-get autoremove -y && \
	apt-get clean && \
	find /var/lib/apt/lists/ -type f -not -name lock -delete; \
	useradd -m -u 1000 -U -s /bin/sh -d /idrp idrp && \
	mkdir -p /idrp/.local/share && \
	mkdir /data && \
	chown -R idrp:idrp /data && \
	ln -s /data /idrp/.local/share/idrp

USER idrp

# check if executable works in this container
RUN /usr/local/bin/idrp --version

EXPOSE 30333 9933 9944 30334 9934 9945
VOLUME ["/data"]

ENTRYPOINT ["/usr/local/bin/idrp"]