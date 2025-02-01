FROM ubuntu:latest AS base

RUN apt update -y && apt upgrade -y
RUN apt-get -y install vim python3-pip
RUN rm /usr/lib/python3.12/EXTERNALLY-MANAGED 


RUN apt-get install -y build-essential bison flex libpcap-dev libpcre3 libpcre3-dev \
                        libdumbnet-dev zlib1g-dev libnghttp2-dev openssl \
                        libphp-adodb php-pear libwww-perl php-gd git autoconf libtool \
                        curl unzip cmake liblzma-dev libssl-dev hwloc libhwloc-dev libpcre2-dev

# FROM the SNORT documentation
WORKDIR /etc
RUN git clone https://github.com/snort3/libdaq.git
WORKDIR /etc/libdaq
RUN ./bootstrap && ./configure && make install
RUN ldconfig

WORKDIR /etc
RUN git clone https://luajit.org/git/luajit.git
WORKDIR /etc/luajit
RUN make && make install
RUN ldconfig



FROM base AS snort
# use build arg here instead of base (multi stage dockerfile)
ARG SNORT_VERSION

# fetch snort binaries
WORKDIR /etc
RUN curl -fL -o /tmp/snort.zip "https://github.com/snort3/snort3/archive/refs/tags/${SNORT_VERSION}.zip"
RUN mkdir /tmp/snort_package && mkdir /etc/snort && unzip /tmp/snort.zip -d /tmp/snort_package
RUN cd /tmp/snort_package/snort3-${SNORT_VERSION} && ./configure_cmake.sh --prefix=/etc/snort 
RUN cd /tmp/snort_package/snort3-${SNORT_VERSION}/build && make -j $(nproc) && make install

WORKDIR /usr/local/bin
RUN ln -s /etc/snort/bin/snort /usr/local/bin/snort
CMD ["tail", "-f", "/dev/null"]

