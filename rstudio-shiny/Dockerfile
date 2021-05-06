FROM rocker/geospatial:4.0.5
MAINTAINER "Ben Best" ben@ecoquants.com

ENV TENSORFLOW_VERSION=gpu
ENV KERAS_VERSION=default

# https://github.com/rocker-org/rocker-versioned2/tree/master/scripts
RUN /rocker_scripts/install_shiny_server.sh
RUN /rocker_scripts/install_python.sh
RUN /rocker_scripts/install_tensorflow.sh

EXPOSE 3838

RUN install2.r --error \
  dbplyr \
  DiagrammeR \
  DT \
  flexdashboard \
  fs \
  glue \
  gt \
  here \
  htmltools \
  httpuv \
  jsonlite \
  kableExtra \
  leaflet \
  mapedit \
  plumber \
  Rcpp \
  reticulate \
  rhandsontable \
  RPostgres \
  shiny \
  shinydashboard \
  shinydashboardPlus \
  shinyjs \
  shinyWidgets \
  units \
  urltools \
  webshot

CMD ["/init"]