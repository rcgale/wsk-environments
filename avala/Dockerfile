FROM rcgale/python_ml

# lapack-dev is available in community repo.
RUN echo "http://dl-4.alpinelinux.org/alpine/edge/community" >> /etc/apk/repositories \
&&  apk add --no-cache \
        g++ \
        lapack-dev \
        gfortran \
&&  pip install --no-cache-dir \
    kaldibin \
    requests \
    pickledonion \
    strategery \
    sortedcontainers \
&& rm -rf ~/.cache/pip

