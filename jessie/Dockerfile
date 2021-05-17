FROM ruby:2.3.8-jessie as ruby_base

FROM buildpack-deps:jessie

# -------------
# for Ruby
# -------------

COPY --from=ruby_base /usr/local/ /usr/local/

ENV RUBY_MAJOR 2.3
ENV RUBY_VERSION 2.3.8

# install things globally, for great justice
# and don't create ".bundle" in all our apps
ENV GEM_HOME /usr/local/bundle
ENV BUNDLE_PATH="$GEM_HOME" \
  BUNDLE_SILENCE_ROOT_WARNING=1 \
  BUNDLE_APP_CONFIG="$GEM_HOME" \
  PATH=$GEM_HOME/bin:$BUNDLE_PATH/gems/bin:$PATH

# -------------
# for Node
# -------------

RUN curl -fsSL https://deb.nodesource.com/gpgkey/nodesource.gpg.key | apt-key add -
RUN curl -sL https://deb.nodesource.com/setup_5.x | bash -
RUN apt-get install -y nodejs
