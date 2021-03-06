FROM debian:stretch-slim

LABEL maintainer "alxchk@gmail.com"

USER 0:0

ENV \
    LC_ALL=C \
    TERM= \
    DEBIAN_FRONTEND=noninteractive \
    PATH=/usr/bin:/usr/sbin:/bin:/sbin:/usr/local/bin \
    BUILDOZER_CACHE=/build/buildozer

RUN \
    echo 'deb http://ftp.debian.org/debian stretch-backports main' >> /etc/apt/sources.list && \
    mkdir /build && dpkg --add-architecture i386 && \
	mkdir -p /usr/share/man/man1/ && \
	apt-get update && apt-get install  --no-install-recommends -y \
	git openjdk-8-jdk build-essential ccache git pkg-config \
	libncurses5:i386 libstdc++6:i386 libgtk2.0-0:i386 libpangox-1.0-0:i386 \
	libpangoxft-1.0-0:i386 libidn11:i386 openjdk-8-jdk unzip zlib1g-dev \
	zlib1g:i386 python-setuptools libssl-dev automake autoconf libtool m4 \
	libltdl-dev libffi-dev python-dev && \
	apt-get install --no-install-recommends -y -t stretch-backports \
	python2.7 python2.7-dev cython python-pip

ADD \
	downloads/android-ndk-r9c-linux-x86_64.tar.bz2 \
	downloads/apache-ant-1.9.4-bin.tar.gz \
	downloads/android-sdk_r20-linux.tgz \
	/root/.buildozer/android/platform/

RUN \
	mv /root/.buildozer/android/platform/android-sdk-linux \
		/root/.buildozer/android/platform/android-sdk-20 && \
	/root/.buildozer/android/platform/android-sdk-20/tools/android list sdk -u -e && \
	/root/.buildozer/android/platform/android-sdk-20/tools/android \
		update sdk -u -a -t tools,platform-tools

ADD downloads/*.zip /build/

RUN \
    mkdir -p /root/project && \
    python -m pip install --upgrade pip setuptools six && \
    cd /build && unzip python-for-android.zip && \
	mv python-for-android-master python-for-android && \
    python -m pip install /build/buildozer.zip && \
	rm -f *.zip && mkdir -p /root/.buildozer/android/platform

COPY bootstrap.spec /build/buildozer.spec

RUN \
	cd /build && \
	yes | buildozer android release && \
	rm -f /root/.buildozer/android/platform/*.bz2 && \
	rm -f /root/.buildozer/android/platform/*.tgz && \
	rm -f /root/.buildozer/android/platform/*.tar.gz && \
	rm -rf /root/.buildozer/android/platform/android-ndk-*/docs && \
	rm -rf /root/.buildozer/android/platform/android-ndk-*/samples && \
	rm -rf $BUILDOZER_CACHE/android/platform/build/build/other_builds

RUN \
    find / -xdev ! -uid 0 -or ! -gid 0 -exec chown 0:0 '{}' ';'

FROM scratch
COPY --from=0 / /

ENV \
    WINEDEBUG="-all" \
    PATH=/usr/bin:/usr/sbin:/bin:/sbin:/usr/local/bin:/build/workspace/project:/root/.local/bin \
    WORKSPACE=/build/workspace \
    PROJECT=/build/workspace/project \
    BUILDOZER_CACHE=/build/buildozer

WORKDIR /build/workspace/project
VOLUME /build/workspace/project

LABEL maintainer "alxchk@gmail.com"
LABEL version="1.0"
LABEL description="Toolchain with buildozer and android-for-linux (custom)"
