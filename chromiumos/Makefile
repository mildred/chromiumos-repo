export PATH:=$(shell pwd)/../depot_tools:$(PATH)

-include config.make

export BOARD

build:
	repo init -u https://chromium.googlesource.com/chromiumos/manifest.git -b $(VERSION)
	repo sync
	# Fix issue
	# https://groups.google.com/a/chromium.org/forum/#!topic/chromium-os-dev/bRPUCFHoBTQ
	# https://bugs.chromium.org/p/chromium/issues/detail?id=508713
	repo download --cherry-pick chromiumos/platform/crosutils 303962/1
	cros_sdk -- ./build_packages --board=$(BOARD)
	cros_sdk -- env USE=kvm ./build_image --board=$(BOARD) --noenable_rootfs_verification --boot_args 'disablevmx=off lsm.module_locking=0'
	dd if=/dev/zero of=$(BOARD).img bs=1M count=2000
	cros flash file://$$PWD/$(BOARD).img src/build/image/$(BOARD)/latest
	rm -rf src/build/image/$(BOARD)

install:
	cp $(BOARD).img $(DESTDIR)/chromiumos-mildred-$(BOARD)-$(VERSION).img

configure:
	:>config.make
	echo BOARD=$(BOARD) >>config.make
	echo VERSION=$(VERSION) >>config.make

.fpm:
	echo "--version=$(VERSION)" >$@

.PHONY: build configure install
