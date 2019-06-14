# -*- mode: ruby -*-
# vi: set ft=ruby :
# -*- mode: ruby -*-
# vi: set ft=ruby :


# Copyright (c) Jupyter Development Team.
# Distributed under the terms of the Modified BSD License.
ARG BASE_CONTAINER=jupyter/scipy-notebook
FROM $BASE_CONTAINER

LABEL maintainer="Jupyter Project <jupyter@googlegroups.com>"

# Set when building on Travis so that certain long-running build steps can
# be skipped to shorten build time.
ARG TEST_ONLY_BUILD

LABEL org.label-schema.license="GPL-2.0" \
      maintainer="Aquabiota Solutions AB <mapcloud@aquabiota.se>"
ARG R_VERSION
ARG NB_USER="jovyan"

USER root

RUN addgroup jovyan staff

#
#
# RUN apt-get update \
# 	&& apt-get install -y --no-install-recommends \
#     gpg \
#     dirmngr \
# 		ed \
# 		less \
# 		locales \
# 		vim-tiny \
# 		wget \
# 		ca-certificates \
# 		fonts-texgyre \
# 	&& rm -rf /var/lib/apt/lists/*
#
# #RUN gpg --keyserver subkeys.pgp.net --recv-keys 7638D0442B90D010  && \
#     #gpg --keyserver subkeys.pgp.net --recv-keys 04EE7237B7D453EC
#
# #RUN gpg -a --export 7638D0442B90D010 | sudo apt-key add -  && \
# #gpg -a --export 04EE7237B7D453EC | sudo apt-key add -
#
# ## Configure default locale, see https://github.com/rocker-org/rocker/issues/19
# RUN echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
# 	&& locale-gen en_US.utf8 \
# 	&& /usr/sbin/update-locale LANG=en_US.UTF-8
#
# ENV LC_ALL en_US.UTF-8
# ENV LANG en_US.UTF-8

#ENV R_BASE_VERSION 3.5.2

ENV R_VERSION=${R_VERSION:-3.5.2} \
    LC_ALL=en_US.UTF-8 \
    LANG=en_US.UTF-8 \
    TERM=xterm


RUN apt-get update \
	&& apt install -y software-properties-common gpg gpg-agent
#
# ## Use Debian unstable via pinning -- new style via APT::Default-Release
# RUN echo "deb http://http.debian.net/debian sid main" > /etc/apt/sources.list.d/debian-unstable.list \
#         && echo 'APT::Default-Release "testing";' > /etc/apt/apt.conf.d/default
#
# RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 7638D0442B90D010
# RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 04EE7237B7D453EC
#
# ## Now install R and littler, and create a link for littler in /usr/local/bin
# RUN apt-get update \
# 	&& apt-get install -y -t unstable --no-install-recommends \
# 		littler \
#                 r-cran-littler \
# 		r-base=${R_BASE_VERSION}-* \
# 		r-base-dev=${R_BASE_VERSION}-* \
# 		r-recommended=${R_BASE_VERSION}-* \
# 	&& ln -s /usr/lib/R/site-library/littler/examples/install.r /usr/local/bin/install.r \
# 	&& ln -s /usr/lib/R/site-library/littler/examples/install2.r /usr/local/bin/install2.r \
# 	&& ln -s /usr/lib/R/site-library/littler/examples/installGithub.r /usr/local/bin/installGithub.r \
# 	&& ln -s /usr/lib/R/site-library/littler/examples/testInstalled.r /usr/local/bin/testInstalled.r \
# 	&& install.r docopt \
# 	&& rm -rf /tmp/downloaded_packages/ /tmp/*.rds \
# 	&& rm -rf /var/lib/apt/lists/*
#
#
#################################

# https://github.com/rocker-org/rocker-versioned/blob/master/r-ver/3.5.2/Dockerfile
RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    bash-completion \
    ca-certificates \
    file \
    fonts-texgyre \
    g++ \
    gfortran \
    gsfonts \
    libblas-dev \
    libbz2-1.0 \
    libcurl3 \
    libicu57 \
    libjpeg62-turbo \
    libopenblas-dev \
    libpangocairo-1.0-0 \
    libpcre3 \
    libpng16-16 \
    libreadline7 \
    libtiff5 \
    liblzma5 \
    locales \
    make \
    unzip \
    zip \
    zlib1g \
  && echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
  && locale-gen en_US.utf8 \
  && /usr/sbin/update-locale LANG=en_US.UTF-8 \
  && BUILDDEPS="curl \
    default-jdk \
    libbz2-dev \
    libcairo2-dev \
    libcurl4-openssl-dev \
    libpango1.0-dev \
    libjpeg-dev \
    libicu-dev \
    libpcre3-dev \
    libpng-dev \
    libreadline-dev \
    libtiff5-dev \
    liblzma-dev \
    libx11-dev \
    libxt-dev \
    perl \
    tcl8.6-dev \
    tk8.6-dev \
    texinfo \
    texlive-extra-utils \
    texlive-fonts-recommended \
    texlive-fonts-extra \
    texlive-latex-recommended \
    x11proto-core-dev \
    xauth \
    xfonts-base \
    xvfb \
    zlib1g-dev" \
  && apt-get install -y --no-install-recommends $BUILDDEPS \
  && cd tmp/ \
  ## Download source code
  && curl -O https://cran.r-project.org/src/base/R-3/R-${R_VERSION}.tar.gz \
  ## Extract source code
  && tar -xf R-${R_VERSION}.tar.gz \
  && cd R-${R_VERSION} \
  ## Set compiler flags
  && R_PAPERSIZE=letter \
    R_BATCHSAVE="--no-save --no-restore" \
    R_BROWSER=xdg-open \
    PAGER=/usr/bin/pager \
    PERL=/usr/bin/perl \
    R_UNZIPCMD=/usr/bin/unzip \
    R_ZIPCMD=/usr/bin/zip \
    R_PRINTCMD=/usr/bin/lpr \
    LIBnn=lib \
    AWK=/usr/bin/awk \
    CFLAGS="-g -O2 -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2 -g" \
    CXXFLAGS="-g -O2 -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2 -g" \
  ## Configure options
  ./configure --enable-R-shlib \
               --enable-memory-profiling \
               --with-readline \
               --with-blas \
               --with-tcltk \
               --disable-nls \
               --with-recommended-packages \
  ## Build and install
  && make \
  && make install \
  ## Add a default CRAN mirror
  && echo "options(repos = c(CRAN = 'https://cran.rstudio.com/'), download.file.method = 'libcurl')" >> /usr/local/lib/R/etc/Rprofile.site \
  ## Add a library directory (for user-installed packages)
  && mkdir -p /usr/local/lib/R/site-library \
  && chown root:staff /usr/local/lib/R/site-library \
  && chmod g+wx /usr/local/lib/R/site-library \
  ## Fix library path
  && echo "R_LIBS_USER='/usr/local/lib/R/site-library'" >> /usr/local/lib/R/etc/Renviron \
  && echo "R_LIBS=\${R_LIBS-'/usr/local/lib/R/site-library:/usr/local/lib/R/library:/usr/lib/R/library'}" >> /usr/local/lib/R/etc/Renviron \
  ## install packages from date-locked MRAN snapshot of CRAN
  && [ -z "$BUILD_DATE" ] && BUILD_DATE=$(TZ="Europe/Stockholm" date -I) || true \
  && MRAN=https://mran.microsoft.com/snapshot/${BUILD_DATE} \
  && echo MRAN=$MRAN >> /etc/environment \
  && export MRAN=$MRAN \
  && echo "options(repos = c(CRAN='$MRAN'), download.file.method = 'libcurl')" >> /usr/local/lib/R/etc/Rprofile.site \
  ## Use littler installation scripts
  && Rscript -e "install.packages(c('littler', 'docopt'), repo = '$MRAN')" \
  && ln -s /usr/local/lib/R/site-library/littler/examples/install2.r /usr/local/bin/install2.r \
  && ln -s /usr/local/lib/R/site-library/littler/examples/installGithub.r /usr/local/bin/installGithub.r \
  && ln -s /usr/local/lib/R/site-library/littler/bin/r /usr/local/bin/r \
  ## Clean up from R source install
  && cd / \
  && rm -rf /tmp/* \
  && apt-get remove --purge -y $BUILDDEPS \
  && apt-get autoremove -y \
  && apt-get autoclean -y \
  && rm -rf /var/lib/apt/lists/*



# ARG R_DIR
#
# ENV R_VERSION=${R_VERSION:-3.5.2} \
#     LC_ALL=en_US.UTF-8 \
#     LANG=en_US.UTF-8 \
#     LANGUAGE=en_US.UTF-8 \
#     TERM=xterm \
#     PATH=/usr/lib/ccache:$PATH \
#     HOME_AQUA=/home/$NB_USER \
#     AQUABIOTA_USER=user.docker \
#     R_DIR=/usr/local/lib/R
#
# #
# USER root
#
# RUN apt-get update && \
#     apt-get install  -y curl
#
# RUN echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
#   && locale-gen en_US.utf8 \
#   && /usr/sbin/update-locale LANG=$LANG LC_ALL=$LC_ALL LANGUAGE=$LANGUAGE \
#   && BUILDDEPS="curl \
#     default-jdk \
#     libbz2-dev \
#     libcairo2-dev \
#     libcurl4-openssl-dev \
#     libpango1.0-dev \
#     libjpeg-dev \
#     libicu-dev \
#     libpcre3-dev \
#     libpng-dev \
#     libreadline-dev \
#     libtiff5-dev \
#     liblzma-dev \
#     libx11-dev \
#     libxt-dev \
#     perl \
#     rsync \
#     subversion tcl8.6-dev \
#     tk8.6-dev \
#     texinfo \
#     texlive-extra-utils \
#     texlive-fonts-recommended \
#     texlive-fonts-extra \
#     texlive-latex-recommended \
#     x11proto-core-dev \
#     xauth \
#     xfonts-base \
#     xvfb \
#     zlib1g-dev" \
#   && apt-get install -y --no-install-recommends $BUILDDEPS \
#   && cd tmp/ \
#   && svn co https://svn.r-project.org/R/trunk R-devel \
#   && cd R-devel \
#   && R_PAPERSIZE=A4 \
#     R_BATCHSAVE="--no-save --no-restore" \
#     R_BROWSER=xdg-open \
#     PAGER=/usr/bin/pager \
#     PERL=/usr/bin/perl \
#     R_UNZIPCMD=/usr/bin/unzip \
#     R_ZIPCMD=/usr/bin/zip \
#     R_PRINTCMD=/usr/bin/lpr \
#     LIBnn=lib \
#     AWK=/usr/bin/awk \
#     CFLAGS="-g -O2 -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2 -g" \
#     CXXFLAGS="-g -O2 -fstack-protector-strong -Wformat -Werror=format-security -Wdate-time -D_FORTIFY_SOURCE=2 -g" \
#   ./configure --enable-R-shlib \
#                --enable-memory-profiling \
#                --with-readline \
#                --with-blas="-lopenblas" \
#                --with-tcltk \
#                --disable-nls \
#                --without-recommended-packages \
#   && make \
#   && make install \
#   && echo "options(repos = c(CRAN = 'https://cran.rstudio.com/'), download.file.method = 'libcurl')" >> /usr/local/lib/R/etc/Rprofile.site \
#   && mkdir -p /usr/local/lib/R/site-library \
#   && chown root:staff /usr/local/lib/R/site-library \
#   && chmod g+wx /usr/local/lib/R/site-library \
#   ## Fix library path
#   && echo "R_LIBS_USER='/usr/local/lib/R/site-library'" >> /usr/local/lib/R/etc/Renviron \
#   && echo "R_LIBS=\${R_LIBS-'/usr/local/lib/R/site-library:/usr/local/lib/R/library:/usr/lib/R/library'}" >> /usr/local/lib/R/etc/Renviron \
#   && [ -z "$BUILD_DATE" ] && BUILD_DATE=$(TZ="Europe/Stockholm" date -I) || true \
#   && MRAN=https://mran.microsoft.com/snapshot/${BUILD_DATE} \
#   && echo MRAN=$MRAN >> /etc/environment \
#   && export MRAN=$MRAN
#   ## MRAN becomes default only in versioned images
#   ## Use littler installation scripts
#
#   RUN R -e "install.packages(c('littler', 'docopt'), repo = '$MRAN')" \
#   && ln -s /usr/local/lib/R/site-library/littler/examples/install2.r /usr/local/bin/install2.r \
#   && ln -s /usr/local/lib/R/site-library/littler/examples/installGithub.r /usr/local/bin/installGithub.r \
#   && ln -s /usr/local/lib/R/site-library/littler/bin/r /usr/local/bin/r \
#   && curl -O /usr/local/bin/install2.r https://github.com/eddelbuettel/littler/raw/master/inst/examples/install2.r \
#   && chmod +x /usr/local/bin/install2.r \
#   && cd / \
#   && rm -rf /tmp/* \
#   && apt-get remove --purge -y $BUILDDEPS \
#   && apt-get autoremove -y \
#   && apt-get autoclean -y \
#   && rm -rf /var/lib/apt/lists/* \
#   && ccache -C
#


#
# # R pre-requisites
# RUN apt-get update && \
#     apt-get install -y --no-install-recommends \
#     fonts-dejavu \
#     tzdata \
#     gfortran \
#     gcc && \
#     rm -rf /var/lib/apt/lists/*
#
#
# LABEL maintainer="AquaBiota  <mapcloud@aquabiota.se>"
# # """
# # USER root
# #
# # # R pre-requisites
# # RUN apt-get update && \
# #     apt-get install -y --no-install-recommends \
# #     fonts-dejavu \
# #     tzdata \
# #     unixodbc \
# #     unixodbc-dev \
# #     #r-cran-rodbc \
# #     gfortran \
# #     gcc && \
# #     pip install -U fortran-magic && \
# #     rm -rf /var/lib/apt/lists/*
# # """
# RUN conda install -c conda-forge r-essentials r-rgdal r-raster r-bookdown \
#    r-PKI r-devtools r-dt  r-reticulate r-rocr gdal simplegeneric rpy2 && \
#    fix-permissions $CONDA_DIR && \
#    fix-permissions /home/$NB_USER
#
# # """
# # #RUN THIS WITHIN THE CONTAINER. THERE WAS AN ERROR: package ‘biomod2’ is not available (for R version 3.5.1). However, it went though before by doing it manual on a running container.
# #
# # # dependencies=TRUE # used for modelling
# # RUN R --quiet -e "install.packages(c('caTools', 'lubridate', 'rjson', 'littler', 'docopt', 'formatR', 'remotes', 'selectr'), repos = c(CRAN = 'https://ftp.acc.umu.se/mirror/CRAN/'))"
# # # repos = c(CRAN = 'https://cran.rstudio.com/')
# #
# # """
# #
# # RUN R --quiet -e "install.packages(c('biomod2'),repos = c(CRAN = 'https://ftp.acc.umu.se/mirror/CRAN/'), dependencies=TRUE )"
# #
# # RUN fix-permissions $CONDA_DIR && \
# #     fix-permissions /home/$NB_USER
