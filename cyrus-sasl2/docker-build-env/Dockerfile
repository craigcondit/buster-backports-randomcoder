FROM debian:buster
MAINTAINER ccondit@randomcoder.org

ADD assets /assets

RUN \
	echo "deb http://ftp.us.debian.org/debian buster main non-free contrib" > /etc/apt/sources.list && \
	echo "deb http://security.debian.org/ buster/updates main contrib non-free" >> /etc/apt/sources.list && \
	echo "deb-src http://ftp.us.debian.org/debian buster main contrib non-free" >> /etc/apt/sources.list && \
	apt-get update && \
	apt-get -y install gnupg2 ca-certificates && \
	apt-key add /assets/bintray-public.key.asc && \
	apt-get -y dist-upgrade && \
	apt-get -y install apt-transport-https && \
	apt-get update && \
	apt-get dist-upgrade && \
	apt-get -y install build-essential devscripts lintian patch patchutils && \
	rm -rf /var/cache/apt && \
	mkdir -p /build /packages

ADD patches /patches

RUN \
	cd /build && \
	apt-get update && \
	apt-get -y build-dep cyrus-sasl2 && \
	apt-get -y source cyrus-sasl2 && \
	rm -rf /var/cache/apt && \
	cd cyrus-* && \
	export DEBEMAIL=ccondit@randomcoder.org DEBFULLNAME="Craig Condit" && \
	dch -b \
		-l "+local" \
		--distribution buster-backports-randomcoder \
		--force-distribution --noquery \
		'Build for Debian buster.' && \
	patch -p1 < /patches/cyrus-sasl2-2.1.27+dfsg-checkpw.patch && \
	dch -b \
		--distribution buster-backports-randomcoder \
		--force-distribution --noquery \
		--increment \
		'Allow crypted passwords.' && \ 
	debian/rules binary && \
	cd /build && \
	cp -f *+local*.deb /packages/ && \
	rm -f /packages/*-dbgsym*.deb

