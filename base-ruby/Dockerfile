FROM xhamir/heroku_ruby_dev:base
MAINTAINER Omar Montalvo <xhamir.one@gmail.com>

ENV NODE_VERSION 0.12.2
ENV NPM_VERSION 2.9.0
ENV RUBY_MAJOR 2.1
ENV RUBY_VERSION 2.1.6
ENV RUBY_DOWNLOAD_SHA256 1e1362ae7427c91fa53dc9c05aee4ee200e2d7d8970a891c5bd76bee28d28be4

RUN gpg --keyserver pool.sks-keyservers.net \
  --recv-keys 7937DFD2AB06298B2293C3187D33FF9D0246406D \
  114F43EE0176B71C7BC219DD50A3051F888C628D


RUN curl -SLO "http://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION-linux-x64.tar.gz" \
	&& curl -SLO "http://nodejs.org/dist/v$NODE_VERSION/SHASUMS256.txt.asc" \
	&& gpg --verify SHASUMS256.txt.asc \
	&& grep " node-v$NODE_VERSION-linux-x64.tar.gz\$" SHASUMS256.txt.asc | sha256sum -c - \
	&& tar -xzf "node-v$NODE_VERSION-linux-x64.tar.gz" -C /usr/local --strip-components=1 \
	&& rm "node-v$NODE_VERSION-linux-x64.tar.gz" SHASUMS256.txt.asc \
	&& npm install -g npm@"$NPM_VERSION" \
	&& npm cache clear


RUN apt-get update \
	&& apt-get install -y bison libgdbm-dev ruby systemtap-sdt-dev \
	&& rm -rf /var/lib/apt/lists/* \
	&& mkdir -p /usr/src/ruby \
	&& curl -fSL -o ruby.tar.gz "http://cache.ruby-lang.org/pub/ruby/$RUBY_MAJOR/ruby-$RUBY_VERSION.tar.gz" \
	&& echo "$RUBY_DOWNLOAD_SHA256 *ruby.tar.gz" | sha256sum -c - \
	&& tar -xzf ruby.tar.gz -C /usr/src/ruby --strip-components=1 \
	&& rm ruby.tar.gz \
	&& cd /usr/src/ruby \
	&& autoconf \
	&& ./configure --disable-install-doc --enable-dtrace \
	&& make -j"$(nproc)" \
	&& make install \
	&& apt-get purge -y --auto-remove bison libgdbm-dev ruby systemtap-sdt-dev \
	&& rm -r /usr/src/ruby

RUN gem install bundler --no-rdoc --no-ri

ENV GEM_HOME /app/vendor/bundle

USER app

WORKDIR /app

ENV BUNDLE_APP_CONFIG=$GEM_HOME PATH=/app/bin:$GEM_HOME/bin:$PATH

CMD ["irb"]
