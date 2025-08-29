FROM r-base:4.5.1

LABEL maintainer="tif.merel@gmail.com"

USER root

# Optional build arguments
ARG USER_ID=1000
ARG GROUP_ID=1000
ARG USER_NAME=ruser
ARG GROUP_NAME=ruser
ARG WORK_DIR

# System dependencies
RUN apt-get update && apt-get install -y \
    libcurl4-openssl-dev \
    libssl-dev \
    libxml2-dev \
    file \
    git \
    bash \
    bash-completion \
    default-jre default-jdk \
    libharfbuzz-dev \
    libfribidi-dev \
    libfontconfig-dev \
    libfontconfig1-dev \
    r-cran-textshaping \
    r-cran-tidyselect \
    r-cran-tidyverse \
    libtiff-dev \
    && rm -rf /var/lib/apt/lists/*

# Ensure site-library exists
RUN mkdir -p /usr/local/lib/R/site-library && \
    chown -R root:staff /usr/local/lib/R/site-library

COPY rip /bin/rip
RUN chmod +x /bin/rip

# Set R library path + repo defaults
ENV R_LIBS_SITE=/usr/local/lib/R/site-library
RUN echo 'options( \
  install.packages.check.source = "no", \
  Ncpus = parallel::detectCores() \
)' >> /usr/lib/R/etc/Rprofile.site

# Install ALL CRAN packages in one layer
RUN rip shiny pacman BiocManager
RUN rip anytime askpass assertthat backports bit bit64 broom bslib
RUN rip cachem cellranger cicerone cli clipr colorspace crayon crosstalk
RUN rip data.table DBI dbplyr digest dplyr DT ellipsis fansi fastmap
RUN rip fontawesome forcats fs gargle generics ggplot2 ggthemes glue
RUN rip googledrive googlesheets4 gtable haven hms htmltools htmlwidgets
RUN rip httpuv httr ids jquerylib jsonlite knitr later lattice lazyeval
RUN rip lifecycle lubridate magrittr mailR markdown Matrix memoise mime
RUN rip modelr munsell nanotime openssl openxlsx pillar pkgconfig plotly
RUN rip plyr pracma promises purrr R6 Rcpp RcppCCTZ readr readxl reprex
RUN rip reshape rjson rlang rsconnect rstudioapi rvest sass scales systemfonts
RUN rip sessioninfo shiny shinyBS shinycssloaders shinydashboard shinyhelper
RUN rip viridisLite visNetwork withr writexl xfun xml2 xtable yaml zoo statmod
RUN rip tidyr tidyselect  tippy tzdb utf8 uuid vctrs ragg textshaping tidyverse 
RUN rip shinyjs shinylogs shinytoastr shinyWidgets stringi stringr tibble

# Install Bioconductor packages (binary if available)
RUN Rscript -e 'BiocManager::install(); BiocManager::install(c( \
  "msa","systemfonts","Biostrings","GenomicFeatures", \
  "GenomicRanges","IRanges","Rsamtools","rtracklayer", \
  "preprocessCore","limma" \
), ask=FALSE, update=TRUE, Ncpus=8)'

# Create user
RUN groupadd -f --gid ${GROUP_ID} ${GROUP_NAME} && \
    useradd -o --uid ${USER_ID} --gid ${GROUP_NAME} -m ${USER_NAME}

ENV EMAIL_AVAILABLE=0

ENTRYPOINT ["/usr/bin/Rscript", "-e", "shiny::runApp('./RokaiXplorer', host='0.0.0.0', port=8000)"]