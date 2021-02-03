ACLOCAL_AMFLAGS = -I m4

SUBDIRS = src

EXTRA_DIST = \
	README.md NEWS AUTHORS FAQ.md COPYING \
	INSTALL_MOODE.md INSTALL_RAUDIO.md INSTALL_VOLUMIO.md \
	mpd_oled.jpg wiring_i2c.png wiring_spi.png \
	\
	moode6_mpd_fifo.patch moode7_mpd_fifo.patch moode_mpd_fifo_old.patch \
	\
	mpd_oled_fifo.conf mpd_oled.service \
        bootstrap commands.txt

docdir = @docdir@
doc_DATA = README.md FAQ.md NEWS AUTHORS COPYING \
	INSTALL_MOODE.md INSTALL_RAUDIO.md INSTALL_VOLUMIO.md \
        mpd_oled.jpg wiring_i2c.png wiring_spi.png

filesdir = ${datarootdir}/${PACKAGE}
files_DATA = mpd_oled_fifo.conf

moodedir = ${filesdir}/moode
moode_DATA = moode6_mpd_fifo.patch moode7_mpd_fifo.patch \
	moode_mpd_fifo_old.patch

bin_SCRIPTS = mpd_oled_install.sh mpd_oled_edit.sh mpd_oled_uninstall.sh

CLEANFILES = $(bin_SCRIPTS)

format_all:
	for f in src/*.cpp src/*.h ; do \
	clang-format -style=file -i $$f; \
	done

