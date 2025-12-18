# syntax=docker/dockerfile:1.7

############################################
# Base image 선택 (아키텍처 자동 분기)
############################################
# FROM ubuntu:24.04
FROM osrf/ros:jazzy-desktop

# korean mirror
USER root
RUN sed -i 's/archive.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list && \
    sed -i 's/security.ubuntu.com/mirror.kakao.com/g' /etc/apt/sources.list

############################################
# 기본 환경 설정
############################################
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Seoul


############################################
# Locale 설정 (ROS 필수)
############################################
# RUN locale-gen en_US.UTF-8
ENV LANG=en_US.UTF-8
ENV LC_ALL=en_US.UTF-8

# 🔑 Universe repo 활성화
# RUN apt-get update && apt-get install -y \
#     software-properties-common \
#     && add-apt-repository universe \
#     && apt-get update

# [추가] apt가 IPv4만 사용하도록 설정
RUN echo 'Acquire::ForceIPv4 "true";' > /etc/apt/apt.conf.d/99force-ipv4

RUN apt-get update -y && \
    apt-get install -y --no-install-recommends \
    curl \
    gnupg2 \
    lsb-release \
    ca-certificates \
    sudo \
    locales \
    git \
    build-essential \
    python3-pip \
    python3-colcon-common-extensions \
    ros-dev-tools \
    && rm -rf /var/lib/apt/lists/*


############################################
# ROS2 Jazzy 저장소 등록
############################################
# RUN curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key \
#     -o /usr/share/keyrings/ros-archive-keyring.gpg

# RUN echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] \
#     http://packages.ros.org/ros2/ubuntu $(lsb_release -cs) main" \
#     > /etc/apt/sources.list.d/ros2.list

############################################
# ROS2 Jazzy 설치
############################################
# RUN apt-get update && apt-get install -y \
#     ros-jazzy-desktop \
#     ros-dev-tools \
#     && rm -rf /var/lib/apt/lists/*

############################################
# 5. 사용자 설정 (Dev Container 권장 사항)
############################################
ARG USERNAME=ubuntu
# Ubuntu 24.04 이미지는 'ubuntu' 사용자가 이미 존재하므로 sudo 권한만 추가
RUN echo "$USERNAME ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

############################################
# rosdep 초기화
############################################
USER $USERNAME
RUN rosdep init || true
RUN rosdep update

############################################
# ROS 환경 자동 source
############################################
# RUN echo "source /opt/ros/jazzy/setup.bash" >> /etc/bash.bashrc
RUN echo "source /opt/ros/jazzy/setup.bash" >> ~/.bashrc
RUN echo "source /usr/share/colcon_common_extensions/lexicon/argcomplete.bash" >> ~/.bashrc

############################################
# Jetson (ARM64) 전용 처리
############################################
ARG TARGETARCH
RUN if [ "$TARGETARCH" = "arm64" ]; then \
        echo "Jetson 환경 감지: ARM64 최적화 설정"; \
        apt-get update && apt-get install -y \
            nvidia-cuda-toolkit \
        || true; \
    fi

############################################
# 7. Jetson (ARM64) 추가 설정 (선택 사항)
############################################
# ARG TARGETARCH
# RUN if [ "$TARGETARCH" = "arm64" ]; then \
#         sudo apt-get update && sudo apt-get install -y \
#         # Jetson에서 필요한 추가 패키지가 있다면 여기에 작성 \
#         && sudo rm -rf /var/lib/apt/lists/*; \
#     fi


############################################
# 기본 작업 디렉토리
############################################
WORKDIR /workspaces/ros2_ws

CMD [ "bash" ]
