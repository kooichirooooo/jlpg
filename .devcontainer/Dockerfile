FROM ubuntu:20.04

# Basic
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME \
    && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
    && apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y git curl wget unzip zsh software-properties-common gnupg2 build-essential vim tzdata \
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

# GitHub CLI
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-key C99B11DEB97541F0 \
    && apt-add-repository https://cli.github.com/packages \
    && apt update \
    && apt install -y gh \
    && apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

USER vscode

WORKDIR /home/vscode

# Julia
RUN git clone https://github.com/JuliaLang/julia ./julia \
    && cd ./julia \
    && make

# Oh My Zsh
RUN wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh || true
RUN git clone https://github.com/zsh-users/zsh-autosuggestions.git $HOME/.oh-my-zsh/custom/plugins/zsh-autosuggestions
RUN git clone https://github.com/zsh-users/zsh-syntax-highlighting.git $HOME/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting
RUN sed -i 's/plugins=(git)/plugins=(git zsh-syntax-highlighting zsh-autosuggestions)/g' ~/.zshrc

# Conda Miniforge
RUN wget https://github.com/conda-forge/miniforge/releases/latest/download/Miniforge3-Linux-aarch64.sh
RUN bash Miniforge3-Linux-aarch64.sh -b && rm Miniforge3-Linux-aarch64.sh

ENV PATH=/home/vscode/miniforge3/bin:/home/vscode/julia/usr/bin:$PATH

RUN conda install -c conda-forge jupyterlab

COPY .devcontainer/gitconfig /home/vscode/.gitconfig
COPY .devcontainer/git.sh /home/vscode/

WORKDIR /workspace

EXPOSE 8888

CMD ["/bin/zsh"]
