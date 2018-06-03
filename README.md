Centos:

  **Step 1:**
    ```# openssl version```
    OpenSSL 1.0.2k-fips  26 Jan 2017
    
   if version is below 1.1:
      ```# sudo yum install libtool perl-core zlib-devel -y
      # curl -O -L https://github.com/openssl/openssl/archive/OpenSSL_1_1_0g.tar.gz
      # tar -zxvf OpenSSL_1_1_0g.tar.gz
      # cd openssl-OpenSSL_1_1_0g
      # ./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl shared zlib
      # make
      # make test
      # sudo make install```

      ```# nano /etc/profile.d/openssl.sh```
      **insert this code and save(ctrl+x):**
      ```# /etc/profile.d/openssl.sh
      pathmunge /usr/local/openssl/bin```

      ```# nano /etc/ld.so.conf.d/openssl-1.1.0g.conf```
      ***insert this code and save(ctrl+x):***
      # /etc/ld.so/conf.d/openssl-1.1.0g.conf
      /usr/local/openssl/lib

      # sudo ldconfig -v
      # openssl version
      OpenSSL 1.1.0h  27 Mar 2018

      replace these lines in Makefile file:
      # nano Makefile

      CFLAGS: -I/usr/local/openssl/include
      LDFLAGS: -L /usr/local/openssl/lib

      to:

      CFLAGS = -I/usr/local/openssl/include -m64 -O3 -std=gnu11 -Wall -mpclmul -march=core2 -mfpmath=sse -mssse3 -fno-strict-aliasing -fno-strict-overflow -fwrapv -DAES=1 -DCOMMIT=\"${COMMIT}\" -D_GNU_SOURCE=1 -D_FILE_OFFSET_BITS=64
      LDFLAGS = -L /usr/local/openssl/lib -m64 -ggdb -rdynamic -lm -lrt -lcrypto -lz -lpthread -lcrypto

      # make clean
      # make
 
  Step 2:
    # yum install git
    # cd ~
    # git clone https://github.com/wecanco/MTProxy.git
    # cd MTProxy
    # make

 Your binary will be objs/bin/mtproto-proxy

to run mtproto-proxy:
  1. Obtain a secret, used to connect to telegram servers. 
  curl -s https://core.telegram.org/getProxySecret -o proxy-secret

  2. Obtain current telegram configuration. It can change (occasionally), so we encourage you to update it once per day.
  curl -s https://core.telegram.org/getProxyConfig -o proxy-multi.conf

  3. Generate a secret to be used by users to connect to your proxy
  head -c 16 /dev/urandom | xxd -ps

  4. Run mtproto-proxy
        mtproto-proxy -u nobody -p 8888 -H 443 -S <secret> --aes-pwd proxy-secret proxy-multi.conf -M 1
  where:
          - nobody is the user name. mtproto-proxy calls setuid() to drop privilegies
          - 443 is the port, used by clients to connect to the proxy
          - 8888 is the local port. You can use it to get statistics from mtproto. Like wget localhost:8888/stats
            You can only get this stat via loopback
          - <secret> is the secret generated at step 3. 
          - proxy-secret and proxy-multi.conf are obtained at steps 1 and 2
          - 1 is the number of workers. You can increase the number of workers, if you have a powerful server
          - also feel free to check out other options using mtproto-proxy help 
             

  5. generate the  link tg://proxy?server=SERVER_NAME&port=443&secret=SECRET

  6.  register your proxy with @MTProxybot on Telegram.

  7. enjoy
