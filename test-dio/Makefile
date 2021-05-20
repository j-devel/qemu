QEMU := ../build/qemu-system-xtensa

all: $(QEMU)

$(QEMU):
	# https://github.com/espressif/qemu/wiki#prerequisites
	sudo apt install libgcrypt-dev 

	#==== https://github.com/espressif/qemu/wiki#configure
	#cd .. && ./configure --target-list=xtensa-softmmu \
		--enable-gcrypt \
		--enable-debug --enable-sanitizers --disable-strip \
		--disable-user --disable-capstone \
		--disable-vnc --disable-sdl --disable-gtk && ninja -C build
	#==== @@ no debug stuff; no libasan; no libpulse
	cd .. && ./configure --target-list=xtensa-softmmu \
		--enable-gcrypt \
		--disable-user --disable-capstone \
		--disable-vnc --disable-sdl --disable-gtk \
		--audio-drv-list=alsa && ninja -C build

strip:
	@echo before strip
	ls -l $(QEMU)
	strip $(QEMU)
	@echo after strip
	ls -l $(QEMU)

run:
	@echo running qemu
	$(QEMU) -nographic -machine esp32 \
		-drive file=./esp32flash.bin,if=mtd,format=raw \
		-nic user,model=open_eth \
		-global driver=timer.esp32.timg,property=wdt_disable,value=true
run-base:
	cp esp32flash.bin--base esp32flash.bin
	make run

TAG ?= zzzz
pack:
	rm -rf qemu
	mkdir -p qemu/bin
	cp $(QEMU) qemu/bin/  # TODO include other qemu-* artifacts
	mkdir -p qemu/share/qemu
	cp ../build/pc-bios/esp32-v3-rom.bin qemu/share/qemu/
	tar cfz qemu-$(TAG).tgz qemu
	rm -rf qemu
