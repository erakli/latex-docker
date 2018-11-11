# fork of https://github.com/blang/latex-docker / Benedikt Lang <mail@blang.io> 
FROM debian:stretch
MAINTAINER Warren MacEvoy <wmacevoy@gmail.com>

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update 

# needed for basic build rules
RUN apt-get install -y software-properties-common

# needed for asymptote to run
RUN apt-get install -y --no-install-recommends \
    freeglut3 \
    libglu1 \
    libosmesa6 \
    ghostscript

RUN apt-get install -y --no-install-recommends \
    build-essential \
    wget \
    libfontconfig1

RUN apt-get clean

RUN rm -rf \
    /var/lib/apt/lists \
    /var/cache/apt/archives \
    /usr/share/doc \
    /usr/share/man

# Install TexLive with scheme-basic
RUN wget http://mirror.ctan.org/systems/texlive/tlnet/install-tl-unx.tar.gz; \
	mkdir /install-tl-unx; \
	tar -xvf install-tl-unx.tar.gz -C /install-tl-unx --strip-components=1; \
    echo "selected_scheme scheme-basic" >> /install-tl-unx/texlive.profile; \
	/install-tl-unx/install-tl -profile /install-tl-unx/texlive.profile; \
    rm -r /install-tl-unx; \
	rm install-tl-unx.tar.gz

ENV TEXLIVE_DIR="/usr/local/texlive/2018"
ENV TEXLIVE_BIN="${TEXLIVE_DIR}/bin/x86_64-linux"

# set TeX Live binaries in front of PATH
ENV PATH="${TEXLIVE_BIN}:${PATH}"

# ghostscript links to export (https://packages.debian.org/stretch/amd64/ghostscript/filelist)
RUN ln -s \
    /usr/bin/dvipdf \
    /usr/bin/eps2eps \
    /usr/bin/font2c \
    /usr/bin/ghostscript \
    /usr/bin/gs \
    /usr/bin/gsbj \
    /usr/bin/gsdj \
    /usr/bin/gsdj500 \
    /usr/bin/gslj \
    /usr/bin/gslp \
    /usr/bin/gsnd \
    /usr/bin/pdf2dsc \
    /usr/bin/pdf2ps \
    /usr/bin/pf2afm \
    /usr/bin/pfbtopfa \
    /usr/bin/pphs \
    /usr/bin/printafm \
    /usr/bin/ps2ascii \
    /usr/bin/ps2epsi \
    /usr/bin/ps2pdf \
    /usr/bin/ps2pdf12 \
    /usr/bin/ps2pdf13 \
    /usr/bin/ps2pdf14 \
    /usr/bin/ps2pdfwr \
    /usr/bin/ps2ps \
    /usr/bin/ps2ps2 \
    /usr/bin/ps2txt \
    /usr/bin/wftopfa \
    "${TEXLIVE_BIN}"

WORKDIR /data
ENV HOME /home

# Install latexmk utililty (https://ctan.org/pkg/latexmk)
RUN tlmgr install latexmk

VOLUME ["/data","/home"]
