# ffmpeg - http://ffmpeg.org/download.html
#
# From https://trac.ffmpeg.org/wiki/CompilationGuide/Ubuntu
#
# https://hub.docker.com/r/jrottenberg/ffmpeg/
#

FROM        ubuntu:18.04 AS base

WORKDIR     /tmp/workdir
RUN         apt -yqq update && \
            apt -yq install --no-install-recommends ca-certificates expat libgomp1 && \
            apt -y autoremove && \
            apt -y clean


FROM        base as build

ARG         PKG_CONFIG_PATH=/opt/ffmpeg/lib/pkgconfig
ARG         LD_LIBRARY_PATH=/opt/ffmpeg/lib
ARG         PREFIX=/opt/ffmpeg
ARG         MAKEFLAGS="-j2"

ENV         FFMPEG_VERSION=4.2.1 \
            AOM_VERSION=1.0.0 \
            FDKAAC_VERSION=0.1.5 \
            FONTCONFIG_VERSION=2.12.4 \
            FREETYPE_VERSION=2.5.5 \
            FRIBIDI_VERSION=0.19.7 \
            KVAZAAR_VERSION=1.2.0 \
            LAME_VERSION=3.100 \
            LIBASS_VERSION=0.13.7 \
            LIBVIDSTAB_VERSION=1.1.0 \
            OGG_VERSION=1.3.2 \
            OPENCOREAMR_VERSION=0.1.5 \
            OPUS_VERSION=1.2 \
            OPENJPEG_VERSION=2.1.2 \
            THEORA_VERSION=1.1.1 \
            VORBIS_VERSION=1.3.5 \
            VPX_VERSION=1.8.0 \
            WEBP_VERSION=1.0.2 \
            X264_VERSION=20170226-2245-stable \
            X265_VERSION=3.1.1 \
            XVID_VERSION=1.3.4 \
            SRC=/usr/local

RUN         buildDeps="autoconf \
                    automake \
                    cmake \
                    curl \
                    bzip2 \
                    libexpat1-dev \
                    g++ \
                    gcc \
                    git \
                    gperf \
                    libtool \
                    make \
                    nasm \
                    perl \
                    pkg-config \
                    python \
                    libssl-dev \
                    yasm \
                    zlib1g-dev" && \
            apt -yqq update && \
            apt -yq install --no-install-recommends ${buildDeps}

## opencore-amr https://sourceforge.net/projects/opencore-amr/
COPY    src/opencore-amr-${OPENCOREAMR_VERSION}.tar.gz /tmp/src.tar.gz
RUN \
        DIR=/tmp/opencore-amr && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        ./configure --prefix="${PREFIX}" --enable-shared  && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

## x264 http://www.videolan.org/developers/x264.html
COPY    src/x264-snapshot-${X264_VERSION}.tar.gz /tmp/src.tar.gz
RUN \
        DIR=/tmp/x264 && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        ./configure --prefix="${PREFIX}" --enable-shared --enable-pic --disable-cli && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

### x265 http://x265.org/
COPY    src/x265_${X265_VERSION}.tar.gz /tmp/src.tar.gz
RUN \
        DIR=/tmp/x265 && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        cd build/linux && \
        sed -i "/-DEXTRA_LIB/ s/$/ -DCMAKE_INSTALL_PREFIX=\${PREFIX}/" multilib.sh && \
        sed -i "/^cmake/ s/$/ -DENABLE_CLI=OFF/" multilib.sh && \
        ./multilib.sh && \
        make -C 8bit install && \
        rm -rf ${DIR} /tmp/src.tar.gz

### libogg https://www.xiph.org/ogg/
COPY    src/libogg-${OGG_VERSION}.tar.gz /tmp/src.tar.gz
RUN \
        DIR=/tmp/ogg && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        ./configure --prefix="${PREFIX}" --enable-shared  && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

### libopus https://www.opus-codec.org/
COPY    src/opus-${OPUS_VERSION}.tar.gz /tmp/src.tar.gz
RUN \
        DIR=/tmp/opus && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        autoreconf -fiv && \
        ./configure --prefix="${PREFIX}" --enable-shared && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

### libvorbis https://xiph.org/vorbis/
COPY    src/libvorbis-${VORBIS_VERSION}.tar.gz /tmp/src.tar.gz
RUN \
        DIR=/tmp/vorbis && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        ./configure --prefix="${PREFIX}" --with-ogg="${PREFIX}" --enable-shared && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

### libtheora http://www.theora.org/
COPY    src/libtheora-${THEORA_VERSION}.tar.gz /tmp/src.tar.gz
RUN \
        DIR=/tmp/theora && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        ./configure --prefix="${PREFIX}" --with-ogg="${PREFIX}" --enable-shared && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

### libvpx https://www.webmproject.org/code/
COPY    src/libvpx-${VPX_VERSION}.tar.gz /tmp/src.tar.gz
RUN \
        DIR=/tmp/vpx && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        ./configure --prefix="${PREFIX}" --enable-vp8 --enable-vp9 --enable-vp9-highbitdepth --enable-pic --enable-shared \
        --disable-debug --disable-examples --disable-docs --disable-install-bins  && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

### libwebp https://developers.google.com/speed/webp/
COPY    src/libwebp-${WEBP_VERSION}.tar.gz /tmp/src.tar.gz
RUN \
        DIR=/tmp/vebp && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        ./configure --prefix="${PREFIX}" --enable-shared  && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

### libmp3lame http://lame.sourceforge.net/
COPY    src/lame-${LAME_VERSION}.tar.gz /tmp/src.tar.gz
RUN \
        DIR=/tmp/lame && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        ./configure --prefix="${PREFIX}" --bindir="${PREFIX}/bin" --enable-shared --enable-nasm --enable-pic --disable-frontend && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

### xvid https://www.xvid.com/
COPY    src/xvidcore-${XVID_VERSION}.tar.gz /tmp/src.tar.gz
RUN \
        DIR=/tmp/xvid && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        cd build/generic && \
        ./configure --prefix="${PREFIX}" --bindir="${PREFIX}/bin" --datadir="${DIR}" --enable-shared --enable-shared && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

### fdk-aac https://github.com/mstorsjo/fdk-aac
COPY    src/fdk-aac-${FDKAAC_VERSION}.tar.gz /tmp/src.tar.gz
RUN \
        DIR=/tmp/fdk-aac && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        autoreconf -fiv && \
        ./configure --prefix="${PREFIX}" --enable-shared --datadir="${DIR}" && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

## openjpeg https://github.com/uclouvain/openjpeg
COPY    src/openjpeg-${OPENJPEG_VERSION}.tar.gz /tmp/src.tar.gz
RUN \
        DIR=/tmp/openjpeg && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        cmake -DBUILD_THIRDPARTY:BOOL=ON -DCMAKE_INSTALL_PREFIX="${PREFIX}" . && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

## freetype https://www.freetype.org/
COPY    src/freetype-${FREETYPE_VERSION}.tar.gz /tmp/src.tar.gz
RUN  \
        DIR=/tmp/freetype && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        ./configure --prefix="${PREFIX}" --disable-static --enable-shared && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

## libvstab https://github.com/georgmartius/vid.stab
COPY    src/vid.stab-${LIBVIDSTAB_VERSION}.tar.gz /tmp/src.tar.gz
RUN  \
        DIR=/tmp/vid.stab && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        cmake -DCMAKE_INSTALL_PREFIX="${PREFIX}" . && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

## fridibi https://www.fribidi.org/
# + https://github.com/fribidi/fribidi/issues/8
COPY    src/fribidi-${FRIBIDI_VERSION}.tar.gz /tmp/src.tar.gz
RUN  \
        DIR=/tmp/fribidi && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        sed -i 's/^SUBDIRS =.*/SUBDIRS=gen.tab charset lib/' Makefile.am && \
        ./bootstrap --no-config && \
        ./configure -prefix="${PREFIX}" --disable-static --enable-shared && \
        make -j 1 && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

## fontconfig https://www.freedesktop.org/wiki/Software/fontconfig/
COPY    src/fontconfig-${FONTCONFIG_VERSION}.tar.gz /tmp/src.tar.gz
RUN  \
        DIR=/tmp/fontconfig && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        ./configure -prefix="${PREFIX}" --disable-static --enable-shared && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

## libass https://github.com/libass/libass
COPY    src/libass-${LIBASS_VERSION}.tar.gz /tmp/src.tar.gz
RUN  \
        DIR=/tmp/libass && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        ./autogen.sh && \
        ./configure -prefix="${PREFIX}" --disable-static --enable-shared && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

## kvazaar https://github.com/ultravideo/kvazaar
COPY    src/kvazaar-${KVAZAAR_VERSION}.tar.gz /tmp/src.tar.gz
RUN \
        DIR=/tmp/kvazaar && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        ./autogen.sh && \
        ./configure -prefix="${PREFIX}" --disable-static --enable-shared && \
        make && \
        make install && \
        rm -rf ${DIR} /tmp/src.tar.gz

## aom
# COPY    src/aom-${AOM_VERSION}.tar.gz /tmp/src.tar.gz
# RUN \
#         DIR=/tmp/aom && \
#         mkdir -p ${DIR} && \
#         cd ${DIR} && \
#         cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
#         rm -rf CMakeCache.txt CMakeFiles && \
#         mkdir aom_build && \
#         cd ./aom_build && \
#         cmake -DCMAKE_INSTALL_PREFIX="${PREFIX}" -DBUILD_SHARED_LIBS=1 .. && \
#         make && \
#         make install && \
#         rm -rf ${DIR} /tmp/src.tar.gz

RUN \
        DIR=/tmp/aom && \
        git clone --branch v${AOM_VERSION} --depth 1 https://aomedia.googlesource.com/aom ${DIR} && \
        cd ${DIR} && \
        rm -rf CMakeCache.txt CMakeFiles && \
        mkdir -p ./aom_build && \
        cd ./aom_build && \
        cmake -DCMAKE_INSTALL_PREFIX="${PREFIX}" -DBUILD_SHARED_LIBS=1 .. && \
        make && \
        make install && \
        rm -rf ${DIR}

## ffmpeg https://ffmpeg.org/
COPY    src/ffmpeg-${FFMPEG_VERSION}.tar.gz /tmp/src.tar.gz
RUN \
        DIR=/tmp/ffmpeg && \
        mkdir -p ${DIR} && \
        cd ${DIR} && \
        cat /tmp/src.tar.gz | tar -zx --strip-components=1 && \
        ./configure \
            --disable-debug \
            --disable-doc \
            --disable-ffplay \
            --enable-shared \
            --enable-avresample \
            --enable-libopencore-amrnb \
            --enable-libopencore-amrwb \
            --enable-gpl \
            --enable-libass \
            --enable-libfreetype \
            --enable-libvidstab \
            --enable-libmp3lame \
            --enable-libopenjpeg \
            --enable-libopus \
            --enable-libtheora \
            --enable-libvorbis \
            --enable-libvpx \
            --enable-libwebp \
            --enable-libx265 \
            --enable-libxvid \
            --enable-libx264 \
            --enable-nonfree \
            --enable-openssl \
            --enable-libfdk_aac \
            --enable-libkvazaar \
            --enable-libaom --extra-libs=-lpthread \
            --enable-postproc \
            --enable-small \
            --enable-version3 \
            --extra-cflags="-I${PREFIX}/include" \
            --extra-ldflags="-L${PREFIX}/lib" \
            --extra-libs=-ldl \
            --prefix="${PREFIX}" && \
        make && \
        make install && \
        make distclean && \
        hash -r && \
        cd tools && \
        make qt-faststart && \
        cp -f qt-faststart ${PREFIX}/bin

## cleanup
RUN \
        ldd ${PREFIX}/bin/ffmpeg | grep opt/ffmpeg | cut -d ' ' -f 3 | xargs -i cp {} /usr/local/lib/ && \
        cp -f ${PREFIX}/bin/* /usr/local/bin/ && \
        cp -rf ${PREFIX}/share/ffmpeg /usr/local/share/ && \
        LD_LIBRARY_PATH=/usr/local/lib ffmpeg -buildconf


FROM        base AS release
MAINTAINER  Julien Rottenberg <julien@rottenberg.info>

ENV         LD_LIBRARY_PATH=/usr/local/lib

COPY        --from=build /usr/local /usr/local/

CMD         ["--help"]
ENTRYPOINT  ["ffmpeg"]
