from apache/airflow:2.10.5

user root

# * Install s2
#
# ** First, the dependencies
run apt update
run apt install -y \
      libgflags-dev \
      libgoogle-glog-dev \
      build-essential \
      libgtest-dev \
      libssl-dev \
      cmake \
      python3-dev \
      python3-distutils-extra \
      python3-pip \
      git \
      g++

# ** Abseil is a C++ Requirement
run git clone https://github.com/abseil/abseil-cpp.git
run cd abseil-cpp \
      && git checkout 20250127.1 \
      && mkdir build \
      && cd build \
      && cmake \
        -D CMAKE_CXX_STANDARD=17 \
        -D CMAKE_POSITION_INDEPENDENT_CODE=ON \
        .. \
      && cmake --build . --target install

run git clone https://github.com/google/s2geometry.git
run cd s2geometry \
      && git checkout v0.12.0
      # && mkdir build \
      # && cd build \
      # && cmake \
      #   -D CMAKE_CXX_STANDARD=17 \
      #   -D BUILD_TESTS=OFF \
      #   .. \
      # && make \
      # && make install

run chown -R airflow s2geometry

user airflow
run cd s2geometry \
      && pip install build \
      && sed -i '23i "-DBUILD_TESTS=OFF", "-DCMAKE_CXX_STANDARD=17",' setup.py \
      && python -m build \
      && pip install "$(find dist -name *.whl)"

run python -c "import s2geometry as s2"

# Install Python requirements
copy requirements.txt /requirements.txt

run pip install --upgrade pip
run pip install --no-cache-dir -r /requirements.txt

