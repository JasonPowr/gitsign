# Build stage
FROM registry.access.redhat.com/ubi9/go-toolset@sha256:7e49e105a854749d67e5e02fb4069b48bd50445098c780b7f808cc351dee2589 AS build-env
WORKDIR /gitsign
RUN git config --global --add safe.directory /gitsign
COPY . .
USER root
RUN make -f Build.mak gitsign-cli-darwin-amd64
RUN make -f Build.mak gitsign-cli-linux-amd64
RUN make -f Build.mak gitsign-cli-windows
RUN gzip gitsign_cli_darwin_amd64
RUN gzip gitsign_cli_linux_amd64
RUN gzip gitsign_cli_windows_amd64.exe
RUN ls -a

# Install Gitsign
FROM registry.access.redhat.com/ubi9/ubi-minimal@sha256:0dfa71a7ec2caf445e7ac6b7422ae67f3518960bd6dbf62a7b77fa7a6cfc02b1

LABEL description="Gitsign is a source code signing tool that leverages simple, secure, and auditable signatures based on simple primitives and best practices."
LABEL io.k8s.description="Gitsign is a source code signing tool that leverages simple, secure, and auditable signatures based on simple primitives and best practices."
LABEL io.k8s.display-name="Gitsign container image for Red Hat Trusted Artifact Signer"
LABEL io.openshift.tags="gitsign trusted-artifact-signer"
LABEL summary="Provides the gitsign CLI binary for signing and verifying container images."
LABEL com.redhat.component="gitsign"

COPY --from=build-env /gitsign/gitsign_cli_darwin_amd64.gz /usr/local/bin/gitsign_cli_darwin_amd64.gz
COPY --from=build-env /gitsign/gitsign_cli_linux_amd64.gz /usr/local/bin/gitsign_cli_linux_amd64.gz
COPY --from=build-env /gitsign/gitsign_cli_windows_amd64.exe.gz /usr/local/bin/gitsign_cli_windows_amd64.exe.gz

RUN chown root:0 /usr/local/bin/gitsign_cli_darwin_amd64.gz && chmod g+wx /usr/local/bin/gitsign_cli_darwin_amd64.gz
RUN chown root:0 /usr/local/bin/gitsign_cli_linux_amd64.gz && chmod g+wx /usr/local/bin/gitsign_cli_linux_amd64.gz
RUN chown root:0 /usr/local/bin/gitsign_cli_windows_amd64.exe.gz && chmod g+wx /usr/local/bin/gitsign_cli_windows_amd64.exe.gz

# Configure home directory
ENV HOME=/home
RUN chgrp -R 0 /${HOME} && chmod -R g=u /${HOME}

WORKDIR ${HOME}

LABEL com.redhat.component="gitsign"
# Makes sure the container stays running
CMD ["tail", "-f", "/dev/null"]
