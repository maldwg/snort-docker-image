FROM ubuntu:latest AS base


ENV DEBIAN_FRONTEND=noninteractive

RUN apt update -y && apt upgrade -y
RUN apt-get install -y --no-install-recommends \
        vim \
        python3-pip


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
RUN git clone https://github.com/LuaJIT/LuaJIT.git
WORKDIR /etc/LuaJIT
RUN make && make install
RUN ldconfig


FROM base AS snort
# use build arg here instead of base (multi stage dockerfile)
ARG SNORT_VERSION
ARG OINKCODE

# fetch snort binaries
WORKDIR /etc
RUN curl -fL -o /tmp/snort.zip "https://github.com/snort3/snort3/archive/refs/tags/${SNORT_VERSION}.zip"
RUN mkdir /tmp/snort_package && mkdir /etc/snort && unzip /tmp/snort.zip -d /tmp/snort_package
WORKDIR /tmp/snort_package/snort3-${SNORT_VERSION}
RUN ./configure_cmake.sh --prefix=/etc/snort 
RUN cd ./build && make -j $(nproc) && make install
# link snort to be able to run it directly
RUN ln -s /etc/snort/bin/snort /usr/local/bin/snort

# install snort3 extras
# installs it into /usr/local/snort/lib per default
RUN curl -fL -o /tmp/snort_extras.zip "https://github.com/snort3/snort3_extra/archive/refs/tags/${SNORT_VERSION}.zip"
RUN unzip /tmp/snort_extras.zip -d /tmp
ENV PKG_CONFIG_PATH=/etc/snort/lib/pkgconfig
RUN cd /tmp/snort3_extra-${SNORT_VERSION} &&./configure_cmake.sh && cd ./build && make && make install

# setup pulled pork for rules
WORKDIR /etc/snort
RUN git clone https://github.com/shirkdog/pulledpork3.git
RUN python3 -m pip install -r pulledpork3/requirements.txt
COPY ./pulledpork.conf /etc/snort/pulledpork3/etc/pulledpork.conf
RUN mkdir -p /etc/snort/etc/rules /etc/snort/etc/so_rules /etc/snort/etc/lists
# add oinkcode and execute pulledpork
ENV OINKCODE=${OINKCODE}
RUN echo "oinkcode = ${OINKCODE}" >> /etc/snort/pulledpork3/etc/pulledpork.conf
RUN /etc/snort/pulledpork3/pulledpork.py -c /etc/snort/pulledpork3/etc/pulledpork.conf
ENV OINKCODE=empty



WORKDIR /usr/local/bin
CMD ["tail", "-f", "/dev/null"]

