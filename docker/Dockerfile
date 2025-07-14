from apache/airflow:2.10.5

# * Install s2
#
# Notes:
#
#   - Fixed abseil version
#   - Explicitly set CXX to 17
#   - Use version 0.12 of s2
#
# ** First, the dependencies; which we need to do this as root.
user root

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

# ** Grab s2geometry now; run as the airflow user as we are pip-installing.
user airflow

run git clone https://github.com/google/s2geometry.git
run cd s2geometry \
      && git checkout v0.12.0

# ** Install it
run cd s2geometry \
      && pip install build \
      # Small hack to set cmake args
      && sed -i '23i "-DBUILD_TESTS=OFF", "-DCMAKE_CXX_STANDARD=17",' setup.py \
      && python -m build \
      && pip install "$(find dist -name *.whl)"

# ** Check it works
run python -c "import s2geometry"


# * Now, back to normal Airflow/Docker business
#
# Install Python requirements
#
# Note: You should _not_ have `s2geometry` as an entry in this file.
copy requirements.txt /requirements.txt

run pip install --upgrade pip
run pip install --no-cache-dir -r /requirements.txt
