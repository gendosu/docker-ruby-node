FROM ruby:2.6.1-alpine3.8 as ruby_base

FROM node:8.16.0-alpine as node_base

FROM alpine:3.8

# skip installing gem documentation
RUN mkdir -p /usr/local/etc \
	&& { \
		echo 'install: --no-document'; \
		echo 'update: --no-document'; \
	} >> /usr/local/etc/gemrc

ENV RUBY_MAJOR 2.6
ENV RUBY_VERSION 2.6.1
ENV RUBY_DOWNLOAD_SHA256 47b629808e9fd44ce1f760cdf3ed14875fc9b19d4f334e82e2cf25cb2898f2f2

# some of ruby's build scripts are written in ruby
#   we purge system ruby later to make sure our final image uses what we just built
# readline-dev vs libedit-dev: https://bugs.ruby-lang.org/issues/11869 and https://github.com/docker-library/ruby/issues/75
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
	&& apk add --no-network --virtual .ruby-rundeps $runDeps \
		bzip2 \
		ca-certificates \
		libffi-dev \
		procps \
		yaml-dev \
		zlib-dev \
        && apk add --upgrade postgresql-dev
#	&& apk del --no-network .ruby-builddeps

# install things globally, for great justice
# and don't create ".bundle" in all our apps
ENV GEM_HOME /usr/local/bundle
ENV BUNDLE_PATH="$GEM_HOME" \
	BUNDLE_SILENCE_ROOT_WARNING=1 \
	BUNDLE_APP_CONFIG="$GEM_HOME"
# path recommendation: https://github.com/bundler/bundler/pull/6469#issuecomment-383235438
ENV PATH $GEM_HOME/bin:$BUNDLE_PATH/gems/bin:$PATH
# adjust permissions of a few directories for running "gem install" as an arbitrary user
RUN mkdir -p "$GEM_HOME" && chmod 777 "$GEM_HOME"
# (BUNDLE_PATH = GEM_HOME, no need to mkdir/chown both)

COPY --from=ruby_base /usr/local/bin/* /usr/local/bin/
COPY --from=ruby_base /usr/local/lib/libruby* /usr/local/lib/
COPY --from=ruby_base /usr/local/lib/ruby /usr/local/lib/ruby
COPY --from=ruby_base /usr/local/include/ruby-2.6.0 /usr/local/include/ruby-2.6.0











ENV NODE_VERSION 8.16.0

RUN addgroup -g 1000 node \
    && adduser -u 1000 -G node -s /bin/sh -D node \
    && apk add --no-cache \
        libstdc++ \
    && apk add --no-cache --virtual .build-deps \
        binutils-gold \
        curl \
        g++ \
        gcc \
        gnupg \
        libgcc \
        linux-headers \
        make \
        python

ENV YARN_VERSION 1.15.2

RUN apk add --no-cache --virtual .build-deps-yarn curl gnupg tar

COPY --from=node_base /usr/local/bin/* /usr/local/bin/
COPY --from=node_base /usr/local/lib/node_modules /usr/local/lib/node_modules
COPY --from=node_base /usr/local/include/node /usr/local/include/node
COPY --from=node_base /opt/yarn-v$YARN_VERSION/bin/* /opt/yarn-v$YARN_VERSION/bin/
COPY --from=node_base /opt/yarn-v$YARN_VERSION/lib/* /opt/yarn-v$YARN_VERSION/lib/
COPY --from=node_base /opt/yarn-v$YARN_VERSION/package.json /opt/yarn-v$YARN_VERSION/package.json
COPY --from=node_base /opt/yarn-v$YARN_VERSION/README.md /opt/yarn-v$YARN_VERSION/README.md
COPY --from=node_base /opt/yarn-v$YARN_VERSION/LICENSE /opt/yarn-v$YARN_VERSION/LICENSE


RUN cd /usr/local/bin; rm npm;     ln -s ../lib/node_modules/npm/bin/npm-cli.js npm
RUN cd /usr/local/bin; rm npx;     ln -s ../lib/node_modules/npm/bin/npx-cli.js npx
RUN cd /usr/local/bin; rm yarn;    ln -s /opt/yarn-v$YARN_VERSION/bin/yarn /usr/local/bin/yarn
RUN cd /usr/local/bin; rm yarnpkg; ln -s /opt/yarn-v$YARN_VERSION/bin/yarnpkg /usr/local/bin/yarnpkg

