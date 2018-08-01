Base on:
https://github.com/tonyho/adbd-linux

openssl:
  src : https://github.com/openssl/openssl.git
  tags: OpenSSL_1_0_2-stable
  compile:
    1. git checkout OpenSSL_1_0_2-stable
    1 ./Configure linux-x86_64 no-asm shared –prefix=/workdir –cross-compile-prefix=arm-linux-
    2. remove -m64
    3. make
