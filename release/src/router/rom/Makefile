include ../common.mak
include $(SRCBASE)/.config

LCMODELNAME = $(shell echo $(BUILD_NAME) | tr '[:upper:]' '[:lower:]')
LJPG = $(wildcard dlna/icon_lrg.$(LCMODELNAME).jpg)
SJPG = $(wildcard dlna/icon_sm.$(LCMODELNAME).jpg)
LPNG = $(wildcard dlna/icon_lrg.$(LCMODELNAME).png)
SPNG = $(wildcard dlna/icon_sm.$(LCMODELNAME).png)
ifeq ($(RTCONFIG_BCMARM),y)
	pkg_type=arm
	DM_VERSION=3.1.0.93
	HTTP_VERSION=3.1.0.86
else ifeq ($(RTCONFIG_QCA),y)
	pkg_type=mipsbig
	DM_VERSION=3.1.0.90
	HTTP_VERSION=3.1.0.83
else
	pkg_type=mipsel
	DM_VERSION=3.1.0.78
	HTTP_VERSION=3.1.0.71
endif

PKG_LIST := readline wxbase expat openssl zlib libevent ncurses libxml2 pcre spawn-fcgi

ifeq ($(RTCONFIG_BCMARM),y)
PKG_LIST += libcurl libsigc++ libpar2
else ifeq ($(RTCONFIG_QCA),y)
# libuclibc++, libcurl, libsigc++, and libpar2 are not required
else
PKG_LIST += libuclibc++ libcurl libsigc++ libpar2
endif

ifeq ($(RTCONFIG_APP_PREINSTALLED),y)
endif

all:
	@echo "" >rom/etc/motd
	@echo "" >>rom/etc/motd
	@echo "ASUSWRT-Merlin `cat ../shared/version`" >>rom/etc/motd

clean:
	rm -f rom/etc/motd

install:
	mkdir -p $(INSTALLDIR)
	( tar --exclude=.svn --exclude=*.bak -cpf - rom ) | ( cd $(INSTALLDIR) && tar -xpf - )

ifeq ($(RTAC88U),y)
	install -m 0644 smb.conf $(INSTALLDIR)/rom/smb.conf
endif
ifeq ($(RTCONFIG_APP_PREINSTALLED),y)
	install -m 0644 optware.asus.$(pkg_type) $(INSTALLDIR)/rom/optware.asus
ifeq ($(RTCONFIG_BCMARM),y)
	install -m 0644 optware.mbwe-bluering $(INSTALLDIR)/rom
else
	install -m 0644 optware.oleg $(INSTALLDIR)/rom
endif
	install -m 0644 packages/asus_base_apps_$(pkg_type).tgz $(INSTALLDIR)/rom # Don't need if installing from Internet.
	-install -m 0644 $(foreach pkg,$(PKG_LIST),packages/$(pkg)_*_$(pkg_type).ipk) $(INSTALLDIR)/rom
	-install -m 0644 packages/downloadmaster_$(DM_VERSION)_$(pkg_type).ipk $(INSTALLDIR)/rom
	-install -m 0644 packages/bzip2_*_$(pkg_type).ipk $(INSTALLDIR)/rom
ifneq ($(HTTP_VERSION),)
	-install -m 0644 packages/asuslighttpd_$(HTTP_VERSION)_$(pkg_type).ipk $(INSTALLDIR)/rom
endif
endif

	mkdir -p $(INSTALLDIR)/usr/sbin
ifeq ($(RTCONFIG_USB),y)
ifneq ($(APP),none)
	install -m 0755 apps_scripts/asusrouter $(INSTALLDIR)/rom/.asusrouter
	install -m 0755 apps_scripts/app_*.sh $(INSTALLDIR)/usr/sbin
	install -m 0755 apps_scripts/check_spectrum.sh $(INSTALLDIR)/usr/sbin
endif
ifeq ($(RTCONFIG_USB_MODEM),y)
	install -m 0644 spn_*.dat $(INSTALLDIR)/rom
	install -m 0755 apps_scripts/find_modem_*.sh $(INSTALLDIR)/usr/sbin
	install -m 0755 apps_scripts/modem_*.sh $(INSTALLDIR)/usr/sbin
	install -m 0755 apps_scripts/3ginfo.sh $(INSTALLDIR)/usr/sbin
ifeq ($(RTCONFIG_INTERNAL_GOBI),y)
ifeq ($(RT4GAC55U),y)
	install -m 0755 apps_scripts/gobi_update.sh $(INSTALLDIR)/usr/sbin
endif
endif
endif
endif

ifeq ($(RTCONFIG_DSL),y)
	cp -f webs_scripts/notif_update.sh $(INSTALLDIR)/usr/sbin/notif_update.sh
endif
	#install -m 0755 webs_scripts/*.sh $(INSTALLDIR)/usr/sbin
ifeq ($(RTCONFIG_AUTOLIVEUPDATE_ZIP),y)
	cp -f webs_scripts/zip_webs_upgrade.sh $(INSTALLDIR)/usr/sbin/webs_upgrade.sh
	cp -f webs_scripts/zip_webs_update.sh $(INSTALLDIR)/usr/sbin/webs_update.sh
ifeq ($(RTCONFIG_BWDPI),y)
	cp -f webs_scripts/sig_upgrade.sh $(INSTALLDIR)/usr/sbin/sig_upgrade.sh
	cp -f webs_scripts/sig_update.sh $(INSTALLDIR)/usr/sbin/sig_update.sh
endif
ifeq ($(RTCONFIG_HTTPS),y)
	cp -f webs_scripts/public.pem $(INSTALLDIR)/usr/sbin/public.pem
endif
else
ifeq ($(RTCONFIG_HTTPS),y)
	cp -f webs_scripts/ssl_webs_upgrade.sh $(INSTALLDIR)/usr/sbin/webs_upgrade.sh
else
	cp -f webs_scripts/nozip_webs_upgrade.sh $(INSTALLDIR)/usr/sbin/webs_upgrade.sh
endif

	cp -f webs_scripts/ssl_webs_update.sh $(INSTALLDIR)/usr/sbin/webs_update.sh

ifeq ($(RTCONFIG_BWDPI),y)
	cp -f webs_scripts/sig_upgrade.sh $(INSTALLDIR)/usr/sbin/sig_upgrade.sh
	cp -f webs_scripts/sig_update.sh $(INSTALLDIR)/usr/sbin/sig_update.sh
endif
ifeq ($(RTCONFIG_HTTPS),y)
	cp -f webs_scripts/public.pem $(INSTALLDIR)/usr/sbin/public.pem
endif
endif

#	Used by Curl
	cp -f others/ca-bundle.crt $(INSTALLDIR)/rom/ca-bundle.crt

ifeq ($(RTCONFIG_OPENVPN),y)
	cp -f others/dh2048.pem	 $(INSTALLDIR)/rom/dh2048.pem
endif

	# remove comments, compress whitespace
	sed 's/#.*//g;s/[ \t]\+/ /g;s/^[ \t]*//;s/[ \t]*$$//;/^$$/d' < rom/etc/services > $(INSTALLDIR)/rom/etc/services
	sed 's/#.*//g;s/[ \t]\+/ /g;s/^[ \t]*//;s/[ \t]*$$//;/^$$/d' < rom/etc/protocols > $(INSTALLDIR)/rom/etc/protocols
	sed 's/#.*//g;s/[ \t]\+/ /g;s/^[ \t]*//;s/[ \t]*$$//;/^$$/d' < rom/etc/ethertypes > $(INSTALLDIR)/rom/etc/ethertypes

ifeq ($(RTCONFIG_HTTPS),y)
	sed 's/#.*//g;s/[ \t]\+/ /g;s/^[ \t]*//;s/[ \t]*$$//;/^$$/d' < rom/etc/openssl.cnf > $(INSTALLDIR)/rom/etc/openssl.cnf
else
	rm -f $(INSTALLDIR)/rom/etc/openssl.cnf
endif

ifeq ($(RTCONFIG_DNSSEC),y)
	cp rom/etc/dnssec-trust-anchors $(INSTALLDIR)/rom/etc/dnssec-trust-anchors
else
	rm -rf $(INSTALLDIR)/rom/etc/dnssec-trust-anchors
endif

ifeq ($(RTCONFIG_RALINK_RT3883),y)
	mkdir -p $(INSTALLDIR)/rom/wlan
	cp -rf wlan/RT3092_PCIe_LNA_2T2R_ALC_V1_2.bin $(INSTALLDIR)/rom/wlan/
	cp -rf wlan/RT3662_AP_2T2R_V0_0.bin $(INSTALLDIR)/rom/wlan/
endif

ifeq ($(RTAC68U),y)
	mkdir -p $(INSTALLDIR)/rom/cfe
	cp -rf cfe/cfe_tmo_2.1.2.6.bin $(INSTALLDIR)/rom/cfe/
	cp -rf cfe/cfe_1.0.2.0.bin $(INSTALLDIR)/rom/cfe
	cp -rf cfe/cfe_1.0.2.0_esmt.bin $(INSTALLDIR)/rom/cfe
	cp -rf cfe/mtd-write $(INSTALLDIR)/rom/cfe/
endif

ifeq ($(RTCONFIG_MEDIA_SERVER),y)
	mkdir -p $(INSTALLDIR)/rom/dlna
ifeq ($(RTCONFIG_INTERNAL_GOBI),y)
	cp -rf dlna/icon_sm.rt-ac68u.jpg $(INSTALLDIR)/rom/dlna/icon_sm.jpg
	cp -rf dlna/icon_sm.rt-ac68u.png $(INSTALLDIR)/rom/dlna/icon_sm.png
	cp -rf dlna/icon_lrg.rt-ac68u.jpg $(INSTALLDIR)/rom/dlna/icon_lrg.jpg
	cp -rf dlna/icon_lrg.rt-ac68u.png $(INSTALLDIR)/rom/dlna/icon_lrg.png
else
ifeq ($(and $(LJPG),$(SJPG),$(LPNG),$(SPNG)),)
	$(warning Add media server icon for $(BUILD_NAME) !!!!!!!!!!!!)
else
	$(foreach f, icon_sm icon_lrg, install -D dlna/$(f).$(LCMODELNAME).jpg $(INSTALLDIR)/rom/dlna/$(f).jpg; \
		install -D dlna/$(f).$(LCMODELNAME).png $(INSTALLDIR)/rom/dlna/$(f).png;)
endif
endif
endif

ifeq ($(RTCONFIG_GETREALIP),y)
	mkdir -p $(INSTALLDIR)/usr/sbin
	install -m 0755 apps_scripts/getrealip.sh $(INSTALLDIR)/usr/sbin/getrealip.sh
endif

