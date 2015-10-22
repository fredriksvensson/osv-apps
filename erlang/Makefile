.PHONY: all

OTP_VERSION=OTP-18.1.1
ERTS_VERSION=7.1
OTP_DIRECTORY=otp_$(OTP_VERSION)
OTP_REPO=https://github.com/erlang/otp.git

all: install erlang.so
module: install erlang.so

$(OTP_DIRECTORY):
	git clone --depth 1 -b $(OTP_VERSION) $(OTP_REPO) $@
	touch $(OTP_DIRECTORY)/lib/os_mon/c_src/disksup.c
	cd $(OTP_DIRECTORY); git am ../patches/*.patch
	cd $(OTP_DIRECTORY); ./otp_build autoconf

configure: $(OTP_DIRECTORY)
	cd $(OTP_DIRECTORY); export ERL_TOP=$(CURDIR)/$(OTP_DIRECTORY) CFLAGS=-fpie LDFLAGS="-pie -rdynamic"; ./otp_build configure -prefix=/usr --libdir=/usr/lib64 --disable-hipe --without-hipe --without-odbc --enable-builtin-zlib --with-termcap=ncurses --without-wx --without-erl_interface --without-javac --without-jinterface --without-debugger --without-et --without-observer


compile: configure
	mkdir -p ROOTFS
	cd $(OTP_DIRECTORY); export ERL_TOP=$(CURDIR)/$(OTP_DIRECTORY); $(MAKE)

install: compile
	cd $(OTP_DIRECTORY); export ERL_TOP=$(CURDIR)/$(OTP_DIRECTORY) DESTDIR=$(CURDIR)/ROOTFS; make install

erlang.so: erlang.c
	$(CC) -shared -rdynamic -fPIC -o $@ -std=gnu99 -DERTS_VERSION=$(ERTS_VERSION) erlang.c

clean:
	-rm -rf ROOTFS
	-rm -rf $(OTP_DIRECTORY)
	-rm -f erlang.so
