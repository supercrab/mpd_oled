ACLOCAL_AMFLAGS = -I m4

SUBDIRS = src

EXTRA_DIST = README.md NEWS AUTHORS COPYING mpd_oled.jpg \
	bootstrap commands.txt \
	\
	doc scripts

docdir = @docdir@
doc_DATA = README.md NEWS AUTHORS COPYING mpd_oled.jpg

docdocdir = ${docdir}/doc
docdoc_DATA = \
	doc/FAQ.md \
	doc/INSTALL_MOODE.md \
	doc/INSTALL_RAUDIO.md \
	doc/INSTALL_VOLUMIO.md \
	doc/wiring_i2c.png \
	doc/wiring_spi.png


filesdir = ${datarootdir}/${PACKAGE}
files_DATA = scripts/mpd_oled_fifo.conf

moodedir = ${filesdir}/moode
moode_DATA = \
	scripts/moode6_mpd_fifo.patch \
	scripts/moode7_mpd_fifo.patch \
	scripts/moode_mpd_fifo_old.patch

bin_SCRIPTS = \
	scripts/mpd_oled_service_install.sh \
	scripts/mpd_oled_service_edit.sh \
	scripts/mpd_oled_service_uninstall.sh \
	scripts/mpd_oled_volumio_mpd_conf_install.sh \
	scripts/mpd_oled_volumio_mpd_conf_uninstall.sh

CLEANFILES = $(bin_SCRIPTS)

format_all:
	for f in src/*.cpp src/*.h ; do \
	clang-format -style=file -i $$f; \
	done

