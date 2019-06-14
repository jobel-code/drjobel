# -*- mode: ruby -*-
# vi: set ft=ruby :
# -*- mode: ruby -*-
# vi: set ft=ruby :
#FROM rocker/geospatial:3.5.2
# docker build -t rinconda .

FROM jupyter/datascience-notebook

#ENV NB_USER jovian
#ENV NB_UID 1000
ENV VENV_DIR /home/${NB_USER}   #/srv/venv

#ENV HOME /home/${NB_USER}
WORKDIR ${HOME}


# Set ENV for all programs...
ENV PATH ${VENV_DIR}/bin:$PATH

USER root
# And set ENV for R! It doesn't read from the environment...
RUN mkdir -p "/home/jovian/R/etc/Renviron"
RUN echo "PATH=${PATH}" >> ${HOME}/R/etc/Renviron # /usr/local/lib/R/etc/Renviron

# The `rsession` binary that is called by nbrsessionproxy to start R doesn't seem to start
# without this being explicitly set
ENV LD_LIBRARY_PATH ${HOME}/R/lib #/usr/local/lib/R/lib


# Create a venv dir owned by unprivileged user & set up notebook in it
# This allows non-root to install python libraries if required
RUN mkdir -p ${VENV_DIR} && chown -R ${NB_USER} ${VENV_DIR} && mkdir -p "/home/jovian/local/bin" && mkdir -p "/home/jovian/local/lib" && mkdir -p "/home/jovian/R/site-library"  && mkdir -p "/home/jovian/R/etc/Renviron"

RUN fix-permissions $LD_LIBRARY_PATH  &&  fix-permissions $VENV_DIR
## Set a default user. Available via runtime flag `--user docker`
## Add user to 'staff' group, granting them write privileges to /usr/local/lib/R/site.library
## User should also have & own a home directory (for rstudio or linked volumes to work properly).
#RUN useradd docker \
#	&& mkdir /home/docker \
#&& chown docker:docker /home/docker \
#&& addgroup docker staff
#RUN addgroup jovian staff


RUN apt-get update \
	&& apt-get install -y --no-install-recommends \
		ed \
		less \
		locales \
		vim-tiny \
		wget \
		ca-certificates \
		fonts-texgyre #\
	#&& rm -rf /var/lib/apt/lists/*


	# Install Python3 packages that aren't available or up-to-date in Conda.
RUN  pip install --quiet -U --upgrade-strategy only-if-needed --no-cache-dir \
	                bs4 \
	                ggplot \
	                lime \
	                s2sphere \
	                jxmlease

COPY amasing_exported_requirements.txt /tmp/

RUN conda install --yes --file /tmp/amasing_exported_requirements.txt && \
	    fix-permissions $CONDA_DIR && \
	    fix-permissions /home/$NB_USER



## Configure default locale, see https://github.com/rocker-org/rocker/issues/19
RUN echo "en_US.UTF-8 UTF-8" >> /etc/locale.gen \
	&& locale-gen en_US.utf8 \
	&& /usr/sbin/update-locale LANG=en_US.UTF-8

ENV LC_ALL en_US.UTF-8
ENV LANG en_US.UTF-8
ENV R_BASE_VERSION 3.5.2

## Now install R and littler, and create a link for littler in /usr/local/bin
RUN apt-get update \
	&& apt-get install -t unstable -y --no-install-recommends \
		littler \
                r-cran-littler \
		r-base=${R_BASE_VERSION}-* \
		r-base-dev=${R_BASE_VERSION}-* \
		r-recommended=${R_BASE_VERSION}-* \
	&& ln -s ${NB_USER}/R/site-library/littler/examples/install.r ${NB_USER}/local/bin/install.r \
	&& ln -s ${NB_USER}/R/site-library/littler/examples/install2.r ${NB_USER}/local/bin/install2.r \
	&& ln -s ${NB_USER}/R/site-library/littler/examples/installGithub.r ${NB_USER}/local/bin/installGithub.r \
	&& ln -s ${NB_USER}/R/site-library/littler/examples/testInstalled.r ${NB_USER}/local/bin/testInstalled.r \
	&& install.r docopt \
	&& rm -rf /tmp/downloaded_packages/ /tmp/*.rds \
	&& rm -rf /var/lib/apt/lists/*

## Remain current
RUN apt-get update -qq \
  	&& apt-get dist-upgrade -y

## From the Build-Depends of the Debian R package, plus subversion
RUN apt-get update -qq \
  	&& apt-get install -t unstable -y --no-install-recommends \
  		bash-completion \
  		bison \
  		debhelper \
  		default-jdk \
  		g++ \
  		gcc \
  		gdb \
  		gfortran \
  		groff-base \
  		libblas-dev \
  		libbz2-dev \
  		libcairo2-dev/unstable \
  		libcurl4-openssl-dev \
  		libjpeg-dev \
  		liblapack-dev \
  		liblzma-dev \
  		libncurses5-dev \
  		libpango1.0-dev \
  		libpcre3-dev \
  		libpng-dev \
  		libreadline-dev \
  		libtiff5-dev \
  		libx11-dev \
  		libxt-dev \
  		mpack \
  		subversion \
  		tcl8.6-dev \
  		texinfo \
  		texlive-base \
  		texlive-extra-utils \
  		texlive-fonts-extra \
  		texlive-fonts-recommended \
  		texlive-generic-recommended \
  		texlive-latex-base \
  		texlive-latex-extra \
  		texlive-latex-recommended \
  		tk8.6-dev \
  		x11proto-core-dev \
  		xauth \
  		xdg-utils \
  		xfonts-base \
  		xvfb \
  		zlib1g-dev


## Set Renviron to get libs from base R install
RUN echo "R_LIBS=\${R_LIBS-'/home/jovian/R/site-library:/home/jovian/R/library:/home/jovian/R/library'}" >> /home/jovian//R/etc/Renviron

  ## Set default CRAN repo
RUN echo 'options(repos = c(CRAN = "https://cran.rstudio.com/"), download.file.method = "libcurl")' >> ${NB_USER}/R/etc/Rprofile.site

RUN cd /home/jovian/bin \
  && mv R Rdevel \
  && mv Rscript Rscriptdevel \
  && ln -s Rdevel RD \
  && ln -s Rscriptdevel RDscript


ARG RSTUDIO_VERSION
#ENV RSTUDIO_VERSION=${RSTUDIO_VERSION:0.1.463}
ARG S6_VERSION
ARG PANDOC_TEMPLATES_VERSION
ENV S6_VERSION=${S6_VERSION:-v1.21.7.0}
ENV S6_BEHAVIOUR_IF_STAGE2_FAILS=2
ENV PATH=/home/jovian/lib/rstudio-server/bin:$PATH
ENV PANDOC_TEMPLATES_VERSION=${PANDOC_TEMPLATES_VERSION:-2.6}

## Download and install RStudio server & dependencies
## Attempts to get detect latest version, otherwise falls back to version given in $VER
## Symlink pandoc, pandoc-citeproc so they are available system-wide
RUN apt-get update \
    && apt-get install -y --no-install-recommends \
      file \
      git \
      libapparmor1 \
      libcurl4-openssl-dev \
      libedit2 \
      libssl-dev \
      lsb-release \
      psmisc \
      procps \
      python-setuptools \
      sudo \
      wget \
    && wget -O libssl1.0.0.deb http://ftp.debian.org/debian/pool/main/o/openssl/libssl1.0.0_1.0.1t-1+deb8u8_amd64.deb \
    && dpkg -i libssl1.0.0.deb \
    && rm libssl1.0.0.deb \
    && RSTUDIO_LATEST=$(wget --no-check-certificate -qO- https://s3.amazonaws.com/rstudio-server/current.ver) \
    && [ -z "$RSTUDIO_VERSION" ] && RSTUDIO_VERSION=$RSTUDIO_LATEST || true \
    && wget -q http://download2.rstudio.org/rstudio-server-${RSTUDIO_VERSION}-amd64.deb \
    && dpkg -i rstudio-server-${RSTUDIO_VERSION}-amd64.deb \
    && rm rstudio-server-*-amd64.deb \
    ## Symlink pandoc & standard pandoc templates for use system-wide
    && ln -s ${NB_USER}/rstudio-server/bin/pandoc/pandoc ${NB_USER}/local/bin \
    && ln -s ${NB_USER}/rstudio-server/bin/pandoc/pandoc-citeproc ${NB_USER}/local/bin \
    && git clone --recursive --branch ${PANDOC_TEMPLATES_VERSION} https://github.com/jgm/pandoc-templates \
    && mkdir -p /opt/pandoc/templates \
    && cp -r pandoc-templates*/* /opt/pandoc/templates && rm -rf pandoc-templates* \
    && mkdir /root/.pandoc && ln -s /opt/pandoc/templates /root/.pandoc/templates \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/ \
    ## RStudio wants an /etc/R, will populate from $R_HOME/etc
    && mkdir -p /etc/R \
    ## Write config files in $R_HOME/etc
    && echo '\n\
      \n# Configure httr to perform out-of-band authentication if HTTR_LOCALHOST \
      \n# is not set since a redirect to localhost may not work depending upon \
      \n# where this Docker container is running. \
      \nif(is.na(Sys.getenv("HTTR_LOCALHOST", unset=NA))) { \
      \n  options(httr_oob_default = TRUE) \
      \n}' >> ${NB_USER}/R/etc/Rprofile.site \
    && echo "PATH=${PATH}" >> ${NB_USER}/R/etc/Renviron \
    ## Need to configure non-root user for RStudio
    && useradd rstudio \
    && echo "rstudio:rstudio" | chpasswd \
  	&& mkdir /home/rstudio \
  	&& chown rstudio:rstudio /home/rstudio \
  	&& addgroup rstudio staff \
    ## Prevent rstudio from deciding to use /usr/bin/R if a user apt-get installs a package
    &&  echo 'rsession-which-r=${NB_USER}/local/bin/R' >> /etc/rstudio/rserver.conf \
    ## use more robust file locking to avoid errors when using shared volumes:
    && echo 'lock-type=advisory' >> /etc/rstudio/file-locks \
    ## configure git not to request password each time
    && git config --system credential.helper 'cache --timeout=3600' \
    && git config --system push.default simple \
    ## Set up S6 init system
    && wget -P /tmp/ https://github.com/just-containers/s6-overlay/releases/download/${S6_VERSION}/s6-overlay-amd64.tar.gz \
    && tar xzf /tmp/s6-overlay-amd64.tar.gz -C / \
    && mkdir -p /etc/services.d/rstudio \
    && echo '#!/usr/bin/with-contenv bash \
            \n## load /etc/environment vars first: \
    		  \n for line in $( cat /etc/environment ) ; do export $line ; done \
            \n exec ${NB_USER}/rstudio-server/bin/rserver --server-daemonize 0' \
            > /etc/services.d/rstudio/run \
    && echo '#!/bin/bash \
            \n rstudio-server stop' \
            > /etc/services.d/rstudio/finish \
    && mkdir -p /home/rstudio/.rstudio/monitored/user-settings \
    && echo 'alwaysSaveHistory="0" \
            \nloadRData="0" \
            \nsaveAction="0"' \
            > /home/rstudio/.rstudio/monitored/user-settings/user-settings \
    && chown -R rstudio:rstudio /home/rstudio/.rstudio

COPY userconf.sh /etc/cont-init.d/userconf

## running with "-e ADD=shiny" adds shiny server
COPY add_shiny.sh /etc/cont-init.d/add
COPY disable_auth_rserver.conf /etc/rstudio/disable_auth_rserver.conf
COPY pam-helper.sh ${NB_USER}/R/studio-server/bin/pam-helper

EXPOSE 8787

  ## automatically link a shared volume for kitematic users
VOLUME /home/rstudio/kitematic

# tidiverse
RUN apt-get update -qq && apt-get -y --no-install-recommends install \
  libxml2-dev \
  libcairo2-dev \
  libsqlite3-dev \
  libmariadbd-dev \
  libmariadb-client-lgpl-dev \
  libpq-dev \
  libssh2-1-dev \
  unixodbc-dev \
  && install2.r --error \
    --deps TRUE \
    tidyverse \
    dplyr \
    devtools \
    formatR \
    remotes \
    selectr \
    caTools \
    BiocManager

# verse
ENV PATH=$PATH:/opt/TinyTeX/bin/x86_64-linux/

## Add LaTeX, rticles and bookdown support
RUN wget "https://travis-bin.yihui.name/texlive-local.deb" \
  && dpkg -i texlive-local.deb \
  && rm texlive-local.deb \
  && apt-get update \
  && apt-get install -y --no-install-recommends \
    ## for rJava
    default-jdk \
    ## Nice Google fonts
    fonts-roboto \
    ## used by some base R plots
    ghostscript \
    ## used to build rJava and other packages
    libbz2-dev \
    libicu-dev \
    liblzma-dev \
    ## system dependency of hunspell (devtools)
    libhunspell-dev \
    ## system dependency of hadley/pkgdown
    libmagick++-dev \
    ## rdf, for redland / linked data
    librdf0-dev \
    ## for V8-based javascript wrappers
    libv8-dev \
    ## R CMD Check wants qpdf to check pdf sizes, or throws a Warning
    qpdf \
    ## For building PDF manuals
    texinfo \
    ## for git via ssh key
    ssh \
 ## just because
    less \
    vim \
 ## parallelization
    libzmq3-dev \
    libopenmpi-dev \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/ \
  ## Use tinytex for LaTeX installation
  && install2.r --error tinytex \
  ## Admin-based install of TinyTeX:
  && wget -qO- \
    "https://github.com/yihui/tinytex/raw/master/tools/install-unx.sh" | \
    sh -s - --admin --no-path \
  && mv ~/.TinyTeX /opt/TinyTeX \
  && /opt/TinyTeX/bin/*/tlmgr path add \
  && tlmgr install metafont mfware inconsolata tex ae parskip listings \
  && tlmgr path add \
  && Rscript -e "tinytex::r_texmf()" \
  && chown -R root:staff /opt/TinyTeX \
  && chown -R root:staff ${NB_USER}/local/lib/R/site-library \
  && chmod -R g+w /opt/TinyTeX \
  && chmod -R g+wx /opt/TinyTeX/bin \
  && echo "PATH=${PATH}" >> ${NB_USER}/R/etc/Renviron \
  && install2.r --error PKI \
  ## And some nice R packages for publishing-related stuff
  && install2.r --error --deps TRUE \
    bookdown rticles rmdshower rJava

# geospatial
RUN apt-get update \
  && apt-get install -y --no-install-recommends \
    lbzip2 \
    libfftw3-dev \
    libgdal-dev \
    libgeos-dev \
    libgsl0-dev \
    libgl1-mesa-dev \
    libglu1-mesa-dev \
    libhdf4-alt-dev \
    libhdf5-dev \
    libjq-dev \
    liblwgeom-dev \
    libproj-dev \
    libprotobuf-dev \
    libnetcdf-dev \
    libsqlite3-dev \
    libssl-dev \
    libudunits2-dev \
    netcdf-bin \
    protobuf-compiler \
    tk-dev \
    unixodbc-dev \
  && install2.r --error \
    RColorBrewer \
    RandomFields \
    RNetCDF \
    classInt \
    deldir \
    gstat \
    hdf5r \
    lidR \
    mapdata \
    maptools \
    mapview \
    ncdf4 \
    proj4 \
    raster \
    rgdal \
    rgeos \
    rlas \
    sf \
    sp \
    spacetime \
    spatstat \
    spdep \
    geoR \
    geosphere \
    ## from bioconductor
    && R -e "BiocManager::install('rhdf5')"

# binder


USER ${NB_USER}


RUN pip install jupyter_contrib_nbextensions && \
    jupyter contrib nbextension install --user && \
    # can modify or enable additional extensions here
    jupyter nbextension enable spellchecker/main --user

RUN pip install jupyter_dashboards faker && \
    jupyter dashboards quick-setup --sys-prefix


RUN R --quiet -e "devtools::install_github('IRkernel/IRkernel')" && \
		    R --quiet -e "IRkernel::installspec(prefix='${VENV_DIR}')" && \
		    R --quiet -e "install.packages('devtools')" && \
		    # python support in RMarkdown
		    R --quiet -e "install.packages('reticulate')" && \
		    # for plotting
		    R --quiet -e "install.packages('ggplot2')" && \
		    # for knitting
		    R --quiet -e "install.packages(c('rmarkdown', 'caTools', 'bitops'))"  && \
		    # dependencies=TRUE # used for modelling
		    R --quiet -e "install.packages(c('DT', 'ROCR', 'caTools', 'lubridate', 'rjson', 'littler', 'docopt', 'formatR', 'remotes', 'selectr'))"

RUN R --quiet -e "install.packages(c('biomod2'), dependencies=TRUE )"


CMD jupyter notebook --ip 0.0.0.0
