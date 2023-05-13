# syntax=docker/dockerfile:1
# See here for image contents: https://github.com/microsoft/vscode-dev-containers/tree/v0.177.0/containers/cpp/.devcontainer/base.Dockerfile

# [Choice] Debian / Ubuntu version: debian-10, debian-9, ubuntu-20.04, ubuntu-18.04
ARG VARIANT="buster"
FROM mcr.microsoft.com/vscode/devcontainers/cpp:0-${VARIANT} as base

FROM base as clone1
WORKDIR repo
RUN git clone https://github.com/zoocide/pts


FROM base as clone2
WORKDIR workspaces/root
RUN git clone --branch llvmorg-15.0.7 --depth 1 https://github.com/llvm/llvm-project.git



FROM base

RUN wget -O - https://apt.llvm.org/llvm-snapshot.gpg.key | apt-key add -
RUN echo "deb http://apt.llvm.org/focal/ llvm-toolchain-focal-15 main" | tee -a /etc/apt/sources.list
RUN apt-get update && apt-get install -y clang-15 llvm-15-dev libclang-15-dev libclang1-15 \
                      zlib1g-dev libomp-15-dev libpolly-15-dev perl libexpat1-dev npm
RUN test -f /usr/lib/x86_64-linux-gnu/libclang-15.so.1 || ln -s /usr/lib/x86_64-linux-gnu/libclang-15.so.15 /usr/lib/x86_64-linux-gnu/libclang-15.so.1
RUN cpan XML::Parser File::chdir Text::Diff Graph Graph::Reader::Dot
RUN node --version | grep -q v10 && npm install -g --unsafe-perm vsce@1.103.1 || npm install -g --unsafe-perm vsce

RUN <<EOF
    wget -O - https://apt.kitware.com/keys/kitware-archive-latest.asc 2>/dev/null | gpg --dearmor - | sudo tee /usr/share/keyrings/kitware-archive-keyring.gpg >/dev/null
    echo 'deb [signed-by=/usr/share/keyrings/kitware-archive-keyring.gpg] https://apt.kitware.com/ubuntu/ focal main' | sudo tee /etc/apt/sources.list.d/kitware.list >/dev/null
    sudo apt-get update
    sudo rm /usr/share/keyrings/kitware-archive-keyring.gpg
    sudo apt-get -y install kitware-archive-keyring
    sudo apt-get update
    sudo apt-get -y install cmake
EOF

# WORKDIR workspaces/root
# RUN git clone --branch llvmorg-15.0.7 --depth 1 https://github.com/llvm/llvm-project.git

# Get external tools.
WORKDIR repo
# RUN git clone https://github.com/zoocide/pts
COPY --link --from=clone1 /repo/ /repo/
COPY --link --from=clone2 /workspaces/root/ /workspaces/root/
