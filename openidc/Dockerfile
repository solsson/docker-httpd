
FROM httpd:2.4.23

ENV OPENIDC_VERSION 1.8.10.1
ENV OPENIDC_VERSION_TGZ_URL https://github.com/pingidentity/mod_auth_openidc/archive/v$OPENIDC_VERSION.tar.gz
ENV OPENIDC_VERSION_TGZ_SHA1 3a5e0faff757cc2922b84881f32d2d4aaa6be4f8

RUN depsRuntime=' \
		libcurl3 ca-certificates \
		libpcre3 \
		libjansson4 \
		libhiredis0.10 \
	' \
  && depsBuild=' \
		curl \
		gcc \
		make \
		libpcre3-dev \
		libssl-dev \
		libcurl4-openssl-dev \
		libjansson-dev \
		pkg-config \
		dh-autoreconf \
		libhiredis-dev \
	' \
	set -x \
	&& apt-get update \
	&& apt-get install -y --no-install-recommends $depsRuntime \
	&& apt-get install -y --no-install-recommends $depsBuild \
	&& rm -r /var/lib/apt/lists/* \
	&& curl -SL "$OPENIDC_VERSION_TGZ_URL" -o mod_auth_openidc-$OPENIDC_VERSION.tar.gz \
	&& echo "$OPENIDC_VERSION_TGZ_SHA1 mod_auth_openidc-$OPENIDC_VERSION.tar.gz" | sha1sum -c - \
	&& mkdir -p src/mod_auth_openidc \
	&& tar -xvf mod_auth_openidc-$OPENIDC_VERSION.tar.gz -C src/mod_auth_openidc --strip-components=1 \
	&& rm mod_auth_openidc-$OPENIDC_VERSION.tar.gz* \
	&& cd src/mod_auth_openidc \
  && ./autogen.sh \
	&& ./configure --with-apxs2=/usr/local/apache2/bin/apxs \
	&& make -j"$(nproc)" \
	&& make install \
	&& /sbin/ldconfig \
	&& cd ../../ \
	&& rm -r src/mod_auth_openidc \
	&& apt-get purge -y --auto-remove $depsBuild

RUN sed -i 's|LoadModule rewrite_module modules/mod_rewrite.so|LoadModule rewrite_module modules/mod_rewrite.so\nLoadModule auth_openidc_module modules/mod_auth_openidc.so|' conf/httpd.conf
