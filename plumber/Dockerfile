# https://www.rplumber.io/articles/hosting.html#docker:~:text=FROM%20rstudio/plumber
# https://github.com/rstudio/plumber/blob/main/Dockerfile
# https://github.com/rocker-org/rocker-versioned2/blob/master/dockerfiles/geospatial_4.4.1.Dockerfile
FROM rocker/geospatial:latest

# install system dependencies
#   plumber deps; `rm` call removes `apt` cache; libpq RPostgres dep
RUN apt-get update -qq && apt-get install -y --no-install-recommends \
  curl git-core libssl-dev libcurl4-gnutls-dev libsodium-dev libxml2-dev \
  libpq-dev && rm -rf /var/lib/apt/lists/*

# install R packages on CRAN; `rm` call removes install2.r's cache
# per R libaries in https://github.com/marinebon/api/blob/main/plumber.R
RUN install2.r --error \
  DBI dbplyr dplyr duckdb fs geojsonsf glue here jsonlite leaflet librarian \
  listviewer mapview plumber readr sf stringr terra tibble \
  && rm -rf /tmp/downloaded_packages

# install R packages on Github 
# RUN installGithub.r \
  # CalCOFI/calcofi4r \
  # && rm -rf /tmp/downloaded_packages/

EXPOSE 8888

CMD Rscript /share/github/marinebon/api/run-api.R

