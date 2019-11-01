FROM openwhisk/python3action

ENV KALDI_ROOT=/kaldi

RUN echo "http://dl-4.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories \
&& apk update && apk add --no-cache make g++ gfortran python2 automake autoconf sox libtool subversion rsync \
&& ln -s $(which gfortran) /usr/local/bin/g77 \
&& git clone https://github.com/kaldi-asr/kaldi.git /kaldi \
&& ( cd /kaldi/tools && make && extras/install_openblas.sh ) \
&& ( \
	cd /kaldi/src \
	&& ./configure --shared --openblas-root=../tools/OpenBLAS/install \
	&& sed -i.bak 's/-DHAVE_EXECINFO_H=1//' kaldi.mk \
	&& sed -i.bak 's/TESTFILES = /TESTFILES = # /' matrix/Makefile \
	&& make depend \
	&& LDFLAGS="-lexecinfo" make \
	) \
&& ( \
		mkdir -p /kaldi_tmp \
	&&	find /kaldi/src -type f -perm -a=x -exec rsync -R {} /kaldi_tmp \; \
	&&	find /kaldi/src -type f -name *.so -exec rsync -R {} /kaldi_tmp \; \
	&&	mkdir -p /kaldi_tmp/kaldi/tools/openfst-1.6.7/lib \
	&&	cp -R /kaldi/tools/openfst-1.6.7/lib/* /kaldi_tmp/kaldi/tools/openfst-1.6.7/lib \
	&&	mkdir -p /kaldi_tmp/kaldi/tools/OpenBLAS/install/lib \
	&&	cp -R /kaldi/tools/OpenBLAS/install/lib/* /kaldi_tmp/kaldi/tools/OpenBLAS/install/lib \
	&&	rm -rf /kaldi \
	&&	mv /kaldi_tmp/kaldi /kaldi \
	&&	rm -rf /kaldi_tmp \
	&&	mkdir -p /kaldi/src/lib \
	&&	find /kaldi/src -type f -name *.so -exec ln -s {} /kaldi/src/lib \; \
	) \
&&	pip --no-cache-dir install kaldibin \
&&	apk del make g++ gfortran python2 automake autoconf sox libtool subversion rsync


FROM rcgale/kaldibin_cpu

RUN echo "http://dl-4.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories \
&& apk update && apk add --no-cache make g++ rsync \
&& rm -rf kaldi_tmp && git clone https://github.com/kaldi-asr/kaldi.git /kaldi_tmp \
&& ( cd /kaldi_tmp/tools && make ) \
&&	mkdir -p /kaldi/tools/openfst-1.6.7/lib && cp -R /kaldi_tmp/tools/openfst-1.6.7/lib/* /kaldi/tools/openfst-1.6.7/lib \
