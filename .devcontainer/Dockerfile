ARG BASE_IMAGE
FROM ${BASE_IMAGE}

# 설정: 미러 교체 + timezone 설정
ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Seoul

RUN sed -i 's|http://archive.ubuntu.com/ubuntu|http://mirror.kakao.com/ubuntu|g' /etc/apt/sources.list && \
    sed -i 's|http://security.ubuntu.com/ubuntu|http://mirror.kakao.com/ubuntu|g' /etc/apt/sources.list && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* && \
    apt-get update && \
    apt-get install -y --no-install-recommends tzdata && \
    ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && \
    echo $TZ > /etc/timezone && \
    dpkg-reconfigure -f noninteractive tzdata && \
    rm -rf /var/lib/apt/lists/*


ENV SHELL=/bin/bash
SHELL ["/bin/bash", "-c"]

#
# ros2-humble
#

ENV ROS_DISTRO=humble
ENV ROS_ROOT=/opt/ros/${ROS_DISTRO}

# set locale
RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
    locales \
  && locale-gen en_US en_US.UTF-8 \
  && update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8 \
  && export LANG=en_US.UTF-8 \
  && locale

# ensure that the ubuntu universe repository is enabled
RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
    software-properties-common \
  && add-apt-repository universe \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean
# add the ros2 gpg key with apt
RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
    curl \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

RUN export ROS_APT_SOURCE_VERSION=$(curl -s https://api.github.com/repos/ros-infrastructure/ros-apt-source/releases/latest | grep -F "tag_name" | awk -F\" '{print $4}') \
  && curl -L -o /tmp/ros2-apt-source.deb "https://github.com/ros-infrastructure/ros-apt-source/releases/download/${ROS_APT_SOURCE_VERSION}/ros2-apt-source_${ROS_APT_SOURCE_VERSION}.$(. /etc/os-release && echo $VERSION_CODENAME)_all.deb" \
  && apt-get install /tmp/ros2-apt-source.deb


# install common packages
RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
    python3-flake8-blind-except \
    python3-flake8-class-newline \
    python3-flake8-deprecated \
    python3-mypy \
    python3-pip \
    python3-pytest \
    python3-pytest-cov \
    python3-pytest-mock \
    python3-pytest-repeat \
    python3-pytest-rerunfailures \
    python3-pytest-runner \
    python3-pytest-timeout \
    ros-dev-tools \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean
# create a workspace and clone all repos
RUN mkdir -p ${ROS_ROOT}/src \
  && vcs import --input https://raw.githubusercontent.com/ros2/ros2/${ROS_DISTRO}/ros2.repos ${ROS_ROOT}/src
# install dependencies using rosdep
RUN apt-get -qq update \
  && rosdep init \
  && rosdep update \
  && rosdep install \
    --from-paths ${ROS_ROOT}/src \
    --ignore-src -y \
    --skip-keys "fastcdr rti-connext-dds-6.0.1 urdfdom_headers"
# build the code in the workspace
RUN cd ${ROS_ROOT}/ \
  && colcon build \
    --symlink-install \
    --cmake-args \
    -DCMAKE_BUILD_TYPE=Release

ENV RMW_IMPLEMENTATION=rmw_cyclonedds_cpp

#
# rqt tools
#

RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
    "ros-${ROS_DISTRO}-rqt-*" \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
      mesa-utils libgl1-mesa-glx libgl1-mesa-dri libglx-mesa0 libegl1-mesa libx11-xcb1 \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

#
# openssh client
#

RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
    openssh-client \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

#
# net-tools, htop
#

RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
    net-tools \
    htop \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

#
# zsh
#

RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
    zsh \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

#
# python
#

RUN apt-get update \
  && apt-get install -y python3-transforms3d ros-humble-tf-transformations ros-humble-vision-msgs \
  && apt-get remove -y python3-sympy \
  && python3 -m pip install --upgrade pip \
  && pip install --upgrade sympy \
  && pip install ultralytics pyquaternion numpy
  # && pip install numpy==1.26.4


# #
# # jetson-stats
# #

# RUN python -m pip install -U jetson-stats

#
# librealsense
#

RUN apt-get -qq update \
  && apt-get upgrade -y \
  && apt-get dist-upgrade -y \
  && apt-get install -y --fix-missing --no-install-recommends \
    libssl-dev \
    libusb-1.0-0-dev \
    libudev-dev \
    pkg-config \
    libgtk-3-dev \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
    wget \
    cmake \
    build-essential \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
    libglfw3-dev \
    libgl1-mesa-dev \
    libglu1-mesa-dev \
    at \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
    udev \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

RUN mkdir -p /etc/apt/keyrings \
  && curl -sSf https://librealsense.intel.com/Debian/librealsense.pgp | \
    sudo tee /etc/apt/keyrings/librealsense.pgp > /dev/null

RUN echo "deb [signed-by=/etc/apt/keyrings/librealsense.pgp] \
  https://librealsense.intel.com/Debian/apt-repo `lsb_release -cs` main" | \
  sudo tee /etc/apt/sources.list.d/librealsense.list

RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
    librealsense2-dev \
    librealsense2-utils \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

COPY ./99-realsense-libusb.rules /etc/udev/rules.d

#
# cv_bridge
#

RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
    ros-${ROS_DISTRO}-cv-bridge \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

#
# diagnostics
#

RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
    ros-${ROS_DISTRO}-diagnostics \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

#
# user-config
#

RUN apt-get -qq update \
  && apt-get install -y --fix-missing --no-install-recommends \
    sudo \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

ARG USER_NAME
ARG USER_UID=1000
ARG USER_GID=${USER_UID}

RUN groupadd --gid ${USER_GID} ${USER_NAME} \
  && useradd --uid ${USER_UID} --gid ${USER_GID} -m ${USER_NAME} \
  && echo "${USER_NAME} ALL=(ALL) NOPASSWD:ALL" > /etc/sudoers.d/${USER_NAME} \
  && echo "${USER_NAME} ALL=(ALL) NOPASSWD:ALL" >> /etc/sudoers \
  && usermod -a -G video ${USER_NAME} \
  && chmod 0440 /etc/sudoers.d/${USER_NAME}
USER ${USER_NAME}

#
# oh-my-zsh
#

RUN wget https://github.com/robbyrussell/oh-my-zsh/raw/master/tools/install.sh -O - | zsh || true \
  && git clone --depth=1 https://github.com/romkatv/powerlevel10k.git   ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/themes/powerlevel10k \
  && git clone https://github.com/supercrabtree/k.git                   ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/k \
  && git clone https://github.com/zsh-users/zsh-autosuggestions.git     ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions \
  && git clone https://github.com/zsh-users/zsh-completions.git         ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-completions \
  && git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

COPY ./.zshrc ./.p10k.zsh /home/${USER_NAME}
ENV SHELL=/bin/zsh

USER root
RUN chown ${USER_NAME}:${USER_NAME} /home/${USER_NAME}/.zshrc /home/${USER_NAME}/.p10k.zsh

#
# thordrive ros2
#

ENV THORDRIVE_ROS_ROOT=/thordrive/ros2

ARG USER_NAME
RUN mkdir -p ${THORDRIVE_ROS_ROOT}/src \
  && chown -R ${USER_NAME}:${USER_NAME} ${THORDRIVE_ROS_ROOT}

#
# user
#

USER ${USER_NAME}

#
# realsense-ros
#

ARG REALSENSE_ROS_VERSION="ros2-master"
ARG REALSENSE_ROS_PATH=${THORDRIVE_ROS_ROOT}/src/realsense-ros
RUN source ${ROS_ROOT}/install/setup.bash \
  && source ${ROS_ROOT}/setup.bash \
  && git clone -b ${REALSENSE_ROS_VERSION} https://github.com/IntelRealSense/realsense-ros.git ${REALSENSE_ROS_PATH} \
  && cd ${THORDRIVE_ROS_ROOT} \
  && colcon build --symlink-install \
  && source install/setup.bash

#
# rplidar_ros
#

ARG RPLIDAR_ROS_VERSION="ros2"
ARG RPLIDAR_ROS_PATH=${THORDRIVE_ROS_ROOT}/src/rplidar_ros
RUN source ${ROS_ROOT}/install/setup.bash \
  && source ${ROS_ROOT}/setup.bash \
  && git clone -b ${RPLIDAR_ROS_VERSION} https://github.com/Slamtec/rplidar_ros.git ${RPLIDAR_ROS_PATH} \
  && cd ${THORDRIVE_ROS_ROOT} \
  && colcon build --symlink-install \
  && source install/setup.bash

COPY ./rplidar.rules /etc/udev/rules.d
