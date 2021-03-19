FROM ruby:2.7-slim-buster

RUN apt-get update -qq \
  && DEBIAN_FRONTEND=noninteractive apt-get install -yq --no-install-recommends \
    build-essential \
    curl \
    git \
  && rm -rf /var/cache/apt/lists/*

ENV APP_USER=waiting_for_dev \
    LANG=C.UTF-8 \
    BUNDLE_JOBS=4 \
    BUNDLE_RETRY=3
ENV GEM_HOME=/home/$APP_USER/gems
ENV APP_HOME=/home/$APP_USER/app

RUN useradd -ms /bin/bash $APP_USER

RUN gem update --system \
  && chown -R $APP_USER:$(id -g $APP_USER) /home/$APP_USER/gems

USER $APP_USER

ENV PATH="/home/$APP_USER/gems/bin/:${PATH}"
RUN gem install jekyll

RUN mkdir -p /home/$APP_USER/history

WORKDIR /home/$APP_USER/app
