FROM ubuntu:latest

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y --no-install-recommends build-essential r-base python3.6 python3-pip python3-setuptools python3-dev

WORKDIR /app

COPY requirements.txt /app/requirements.txt

RUN yes w | pip3 install -r requirements.txt
RUN apt-get install libcurl4-openssl-dev
RUN apt-get install libssl-dev
RUN  apt-get -y  install libgit2-dev
RUN apt-get -y install libxml2-dev

#COPY rpack/curl_3.3.tar.gz /app/curl_3.3.tar.gz
#COPY rpack/git2r_0.26.1.tar.gz /app/git2r_0.26.1.tar.gz
COPY rpack/* /app/

RUN Rscript -e "install.packages(c('bit_1.1-14.tar.gz',  'pkgconfig_2.0.2.tar.gz', 'plogr_0.2.0.tar.gz', 'DBI_1.0.0.tar.gz','R6_2.4.0.tar.gz','Rcpp_1.0.2.tar.gz','assertthat_0.2.1.tar.gz','curl_3.3.tar.gz','data.table_1.12.2.tar.gz','git2r_0.26.1.tar.gz','glue_1.3.1.tar.gz','iterators_1.0.10.tar.gz','jsonlite_1.6.tar.gz','magrittr_1.5.tar.gz','mime_0.7.tar.gz','sys_3.2.tar.gz','stringi_1.4.3.tar.gz','foreach_1.4.7.tar.gz'),repos=NULL,type='source')"

RUN Rscript -e "install.packages('rlang_0.4.0.tar.gz',repos=NULL,type='source')" 


RUN Rscript -e "install.packages(c('stringr_1.4.0.tar.gz','backports_1.1.4.tar.gz','ellipsis_0.2.0.1.tar.gz','digest_0.6.20.tar.gz','zeallot_0.1.0.tar.gz'),repos=NULL,type='source')" 
RUN Rscript -e "install.packages(c('lubridate_1.7.4.tar.gz','askpass_1.1.tar.gz','purrr_0.3.2.tar.gz', 'bit64_0.9-7.tar.gz','prettyunits_1.0.2.tar.gz','vctrs_0.2.0.tar.gz','memoise_1.1.0.tar.gz'),repos=NULL,type='source')" 

RUN Rscript -e "install.packages(c('tidyselect_0.2.5.tar.gz','blob_1.2.0.tar.gz'),repos=NULL,type='source')" 



RUN Rscript -e "install.packages('glue_1.3.1.tar.gz',repos=NULL,type='source')" 
RUN Rscript -e "install.packages(c('BH_1.69.0-1.tar.gz','crayon_1.3.4.tar.gz','utf8_1.1.4.tar.gz'),repos=NULL,type='source')" 
RUN Rscript -e "install.packages(c('cli_1.1.0.tar.gz', 'fansi_0.4.0.tar.gz', 'pillar_1.4.2.tar.gz'),repos=NULL,type='source')"
RUN Rscript -e "install.packages('tibble_2.1.3.tar.gz',repos=NULL,type='source')" 
RUN Rscript -e "install.packages(c('dplyr_0.8.3.tar.gz'),repos=NULL,type='source')"

RUN Rscript -e "install.packages(c('dbplyr_1.4.2.tar.gz'),repos=NULL,type='source')" 


RUN Rscript -e "install.packages('tidyr_0.8.3.tar.gz',repos=NULL,type='source')" 

RUN Rscript -e "install.packages('Rcpp_1.0.2.tar.gz',repos=NULL,type='source')" 
RUN Rscript -e "install.packages('RSQLite_2.1.2.tar.gz',repos=NULL,type='source')" 

RUN Rscript -e "install.packages('rplexos.tar.gz',repos=NULL,type='source')" 

