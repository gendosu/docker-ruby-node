FROM ruby:2.7.2-buster as ruby_base

FROM node:12.18.3-buster as node_base

FROM buildpack-deps:stretch

# -------------
# for Ruby
# -------------

COPY --from=ruby_base /usr/local/ /usr/local/

ENV RUBY_MAJOR 2.7
ENV RUBY_VERSION 2.7.2

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

ENV NODE_VERSION 12.18.3
ENV YARN_VERSION 1.22.4

COPY --from=node_base /usr/local/ /usr/local/
COPY --from=node_base /opt/ /opt/

RUN cd /usr/local/bin \
  && rm nodejs npm npx yarn yarnpkg \
  && ln -s ../lib/node_modules/npm/bin/npm-cli.js npm \
  && ln -s ../lib/node_modules/npm/bin/npx-cli.js npx \
  && ln -s /opt/yarn-v$YARN_VERSION/bin/yarn /usr/local/bin/yarn \
  && ln -s /opt/yarn-v$YARN_VERSION/bin/yarnpkg /usr/local/bin/yarnpkg \
  && ln -s /usr/local/bin/node /usr/local/bin/nodejs \
  && groupadd --gid 1000 node \
  && useradd --uid 1000 --gid node --shell /bin/bash --create-home node
