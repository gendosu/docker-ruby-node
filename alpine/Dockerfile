FROM ruby:3.0.2-alpine3.13 as ruby_base

FROM node:16.5.0-alpine3.13 as node_base

FROM alpine:3.13

# -------------
# for Ruby
# -------------

COPY --from=ruby_base /usr/local/ /usr/local/

ENV RUBY_MAJOR 3.0
ENV RUBY_VERSION 3.0.2

RUN set -ex \
	\
	&& apk add --no-cache --virtual .ruby-builddeps \
	autoconf \
	bison \
	bzip2 \
	bzip2-dev \
	ca-certificates \
	coreutils \
	dpkg-dev dpkg \
	gcc \
	gdbm-dev \
	glib-dev \
	libc-dev \
	libffi-dev \
	libxml2-dev \
	libxslt-dev \
	linux-headers \
	make \
	ncurses-dev \
	libressl \
	libressl-dev \
	procps \
	readline-dev \
	ruby \
	tar \
	xz \
	yaml-dev \
	tzdata \
	zlib-dev \
	postgresql-dev
# && apk del --no-network .ruby-builddeps

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

ENV NODE_VERSION 16.5.0
ENV YARN_VERSION 1.22.5

COPY --from=node_base /usr/local/ /usr/local/
COPY --from=node_base /opt/ /opt/

RUN cd /usr/local/bin \
	&& rm npm npx yarn yarnpkg \
	&& ln -s ../lib/node_modules/npm/bin/npm-cli.js npm \
	&& ln -s ../lib/node_modules/npm/bin/npx-cli.js npx \
	&& ln -s /opt/yarn-v$YARN_VERSION/bin/yarn /usr/local/bin/yarn \
	&& ln -s /opt/yarn-v$YARN_VERSION/bin/yarnpkg /usr/local/bin/yarnpkg \
	&& addgroup -g 1000 node \
	&& adduser -u 1000 -G node -s /bin/sh -D node
