include ../common.mak

#BUILD_READMEM=y

CFLAGS += -Wall -O2 $(EXTRACFLAGS)
CFLAGS += -I. -I$(TOP)/shared -I$(SRCBASE)/include -idirafter$(LINUXDIR)/include
CFLAGS += -I$(TOP)/openssl/include
CFLAGS += -I$(TOP)/push_log
ifeq ($(RTCONFIG_DPSTA),y)
CFLAGS += -I$(TOP)/dpsta
endif
CFLAGS += -ffunction-sections -fdata-sections
CFLAGS += -DDLM -DASUS_DDNS -DASUS_EXT -DBTN_SETUP -DWEB_REDIRECT -DRTCONFIG_CROND

CFLAGS += -DDEBUG_RCTEST
CFLAGS += -DQCA_WIFI_INS_RM

ifeq ($(RTCONFIG_WPS),y)
CFLAGS += -I$(SRCBASE)/wps/brcm_apps/include
endif

ifeq ($(RTCONFIG_RALINK),y)
CFLAGS += -I$(TOP)/shared/sysdeps/ralink -I$(TOP)/wireless_tools
ifneq ($(findstring MAC_REPEATER=, $(shell cat $(LINUXDIR)/.config)),)
export CFLAGS += -DMAC_REPEATER
endif
ifneq ($(findstring CONFIG_RT2860V2_AP_EDCCA_MONITOR=, $(shell cat $(LINUXDIR)/.config)),)
export CFLAGS += -DCE_ADAPTIVITY
endif
endif

ifeq ($(RTCONFIG_QCA),y)
CFLAGS += -I$(TOP)/shared/sysdeps/qca -I$(TOP)/wireless_tools
endif

include $(SRCBASE)/.config

LDFLAGS = -L$(TOP)/nvram${BCMEX} -lnvram -L$(TOP)/shared -lshared -lcrypt -L$(TOP)/libbcmcrypto -lbcmcrypto -ldl

ifeq ($(RTCONFIG_BCMARM),y)
CFLAGS += -I$(SRCBASE)/shared/bcmwifi/include -DHAVE_TYPE_FLOAT
CFLAGS += -I$(SRCBASE)/common/include
LDFLAGS += -L$(TOP)/libbcm -lbcm
LDFLAGS += -lgcc_s
ifeq ($(RTCONFIG_QTN),y)
CFLAGS += -I$(TOP)/libqcsapi_client -I$(TOP)/shared/sysdeps/qtn
LDFLAGS += -L$(TOP)/libqcsapi_client -lqcsapi_client
endif
endif

ifeq ($(RTCONFIG_HTTPS),y)
CFLAGS += -I$(TOP)/openssl/include/openssl
LDFLAGS += -L$(TOP)/openssl -lcrypto -lssl
endif

ifeq ($(RTCONFIG_BWDPI),y)
CFLAGS += -I$(TOP)/bwdpi/
CFLAGS += -I$(TOP)/httpd/
CFLAGS += -I$(TOP)/util-linux/include
LDFLAGS += -L$(TOP)/bwdpi -lbwdpi
endif

ifeq ($(RTCONFIG_SPEEDTEST),y)
LDFLAGS += -lm
LDFLAGS += -lpthread
endif

ifeq ($(RTCONFIG_BCMSMP),y)
CFLAGS += -DSMP
endif

ifeq ($(RTCONFIG_4352_5G),y)
CFLAGS += -DBCM4352
endif

ifeq ($(RTCONFIG_USB),y)
CFLAGS += -I$(TOP)/libdisk
CFLAGS += -DU2EC
LDFLAGS += -L$(TOP)/libdisk -ldisk
endif

ifeq ($(RTCONFIG_PUSH_EMAIL),y)
LDFLAGS += -L$(TOP)/openssl -lcrypto -lssl
LDFLAGS += -L$(TOP)/curl-7.21.7/lib/.libs -lcurl
LDFLAGS += -L$(TOP)/libxml2/.libs -lxml2
LDFLAGS += -L$(TOP)/wb -lws
LDFLAGS += -L$(TOP)/push_log -lpush_log
endif

ifeq ($(RTCONFIG_ODMPID),y)
CFLAG += RTCONFIG_ODMPID
endif

#CFLAGS += -DRC_BUILDTIME=$(shell date -d "now" +"%s")

ifneq ($(findstring NET_CLS_POLICE=, $(shell cat $(LINUXDIR)/.config)),)
else
ifneq ($(findstring IMQ=, $(shell cat $(LINUXDIR)/.config)),)
export CFLAGS += -DCLS_ACT
endif
endif

ifneq ($(findstring DEVTMPFS=, $(shell cat $(LINUXDIR)/.config)),)
export CFLAGS += -DDEVTMPFS
endif

ifeq ($(RA_SKU),y)
export CFLAGS += -DRA_SINGLE_SKU
endif

LDFLAGS += -ffunction-sections -fdata-sections -Wl,--gc-sections
ifeq ($(STATIC),1)
LDFLAGS += -static
endif
LDFLAGS += $(EXTRA_LDFLAGS)

OBJS := rc.o init.o interface.o lan.o wireless.o wan.o pppd.o auth.o services.o mtd.o
OBJS += firewall.o ppp.o services.o common.o
OBJS += watchdog.o ntp.o btnsetup.o qos.o udhcpc.o ate.o #cpu.o
ifeq ($(RTCONFIG_FANCTRL),y)
OBJS += phy_tempsense.o
endif
ifeq ($(RTCONFIG_BCMARM),y)
OBJS += bcmutils.o
endif

ifeq ($(RTCONFIG_RALINK),y)
OBJS += init-ralink.o wps-ralink.o ralink.o
ifeq ($(RTCONFIG_DSL),y)
OBJS += init-ralink-dsl.o 
endif
else ifeq ($(RTCONFIG_QCA),y)
OBJS += init-qca.o wps-qca.o qca.o $(if $(wildcard sysdeps/qca/ctl.c),ctl.o,prebuild/ctl.o)
else
OBJS += init-broadcom.o wps-broadcom.o broadcom.o
ifeq ($(RTCONFIG_DSL),y)
OBJS += init-broadcom-dsl.o
endif
endif

# TODO, speedtest
ifeq ($(RTCONFIG_SPEEDTEST),y)
OBJS += speedtest.o
endif

OBJS += wanduck.o
OBJS += tcpcheck.o

ifeq ($(BUILD_READMEM),y)
CFLAGS += -DBUILD_READMEM
OBJS += readmem.o
endif

ifeq ($(RTCONFIG_USB),y)
OBJS += usb.o
OBJS += usbled.o
OBJS += usb_devices.o
endif

ifeq ($(RTCONFIG_JFFS2),y)
OBJS += jffs2.o
endif

ifeq ($(RTCONFIG_BRCM_NAND_JFFS2),y)
OBJS += jffs2.o
endif

ifeq ($(RTCONFIG_UBIFS),y)
OBJS += ubifs.o
endif

ifeq ($(RTCONFIG_CIFS),y)
OBJS += cifs.o
endif

ifeq ($(RTCONFIG_SSH),y)
OBJS += ssh.o
endif

ifeq ($(RTCONFIG_PPTPD),y)
OBJS += vpn.o
endif

ifeq ($(RTCONFIG_ACCEL_PPTPD),y)
OBJS += vpn.o
endif

ifeq ($(RTCONFIG_SNMPD),y)
OBJS += snmpd.o
endif
ifeq ($(RTCONFIG_OPENVPN),y)
CFLAGS += -DRTCONFIG_CROND
OBJS += openvpn.o
endif

ifeq ($(RTCONFIG_OLD_PARENTALCTRL),y)
OBJS += parental_ctrl.o
endif

ifeq ($(RTCONFIG_PARENTALCTRL),y)
OBJS += pc.o
OBJS += pc_block.o
endif

ifeq ($(RTCONFIG_DSL),y)
OBJS += dsl.o
ifeq ($(RTCONFIG_RALINK),y)
OBJS += dsl-upg.o
endif
ifeq ($(RTCONFIG_DSL_TCLINUX),y)
OBJS += dsl_fb.o
OBJS += dsl_diag.o
endif
endif

ifeq ($(RTCONFIG_BCMWL6),y)
ifeq ($(RTCONFIG_PROXYSTA),y)
OBJS += psta_monitor.o
endif
endif

ifeq ($(RTCONFIG_WPS),y)
OBJS += wpsaide.o
endif

ifeq ($(RTN65U),y)
OBJS += asm1042.o
endif

ifeq ($(RTCONFIG_VPNC),y)
OBJS += vpnc.o
endif

ifeq ($(RTCONFIG_SHP),y)
CFLAGS += -DSHP
endif

ifeq ($(RTCONFIG_TIMEMACHINE),y)
OBJS += timemachine.o
endif

CFLAGS += $(if $(RTCONFIG_BLINK_LED),-I$(TOP)/bled)

export CFLAGS

ifeq ($(RTCONFIG_QTN),y)
OBJS += qtn.o
OBJS += find_host_addr.o
OBJS += qtn_monitor.o
endif

ifeq ($(RTCONFIG_BWDPI),y)
OBJS += bwdpi.o
OBJS += bwdpi_monitor.o
OBJS += bwdpi_check.o
OBJS += bwdpi_wred_alive.o
endif

ifeq ($(RTCONFIG_IPERF),y)
OBJS += monitor.o
endif

ifeq ($(RTCONFIG_HTTPS),y)
OBJS += pwdec.o
endif

ifeq ($(RT4GAC55U),y)
OBJS += lteled.o
endif

ifeq ($(RTAC68U),y)
OBJS += sysdeps/broadcom/cfe.o
endif

ifeq ($(RTCONFIG_TR069),y)
OBJS += tr069.o
endif

ifeq ($(RTCONFIG_BCMARM),y)
ifeq ($(RTCONFIG_QTN),y)
vpath %.c sysdeps sysdeps/broadcom sysdeps/qtn $(TOP)/libqcsapi_client/qcsapi_rpc_common/client $(TOP)/sysdeps/qtn $(SRCBASE)/shared
else
vpath %.c sysdeps sysdeps/broadcom $(SRCBASE)/shared
endif
else ifeq ($(RTCONFIG_RALINK),y)
vpath %.c sysdeps sysdeps/ralink
else ifeq ($(RTCONFIG_QCA),y)
vpath %.c sysdeps sysdeps/qca
else
vpath %.c sysdeps sysdeps/broadcom
endif

all: rc

rc: $(OBJS)
	@echo " [rc] CC $@"
	@$(CC) -o $@ $^ $(LDFLAGS) $(CFLAGS)

	$(SIZECHECK)
	$(CPTMP)

install: all
	@echo "[rc] Installing..."
	@install -D rc $(INSTALLDIR)/sbin/rc
	@$(STRIP) $(INSTALLDIR)/sbin/rc
	@chmod 0500 $(INSTALLDIR)/sbin/rc

ifeq ($(RTCONFIG_BCMARM),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc preinit
else
	@cd $(INSTALLDIR)/sbin && ln -sf rc init
endif
	@cd $(INSTALLDIR)/sbin && ln -sf rc console
	@cd $(INSTALLDIR)/sbin && ln -sf rc hotplug
	@cd $(INSTALLDIR)/sbin && ln -sf rc service
	@cd $(INSTALLDIR)/sbin && ln -sf rc rcheck
	@cd $(INSTALLDIR)/sbin && ln -sf rc radio
	@cd $(INSTALLDIR)/sbin && ln -sf rc led_ctrl
	@cd $(INSTALLDIR)/sbin && ln -sf rc reboot
	@cd $(INSTALLDIR)/sbin && ln -sf rc halt
ifeq ($(RTCONFIG_BCMARM),y)
#	@cd $(INSTALLDIR)/sbin && ln -sf rc gpio
	@cd $(INSTALLDIR)/sbin && ln -sf rc mtd-write2
	@cd $(INSTALLDIR)/sbin && ln -sf rc mtd-erase2
else
	@cd $(INSTALLDIR)/sbin && ln -sf rc mtd-write
	@cd $(INSTALLDIR)/sbin && ln -sf rc mtd-erase
endif
	@cd $(INSTALLDIR)/sbin && ln -sf rc mtd-unlock
	@cd $(INSTALLDIR)/sbin && ln -sf rc ots
	@cd $(INSTALLDIR)/sbin && ln -sf rc watchdog
ifeq ($(RTCONFIG_FANCTRL),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc phy_tempsense
endif
ifeq ($(RTCONFIG_BCMWL6),y)
ifeq ($(RTCONFIG_PROXYSTA),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc psta_monitor
endif
endif
ifeq ($(RTCONFIG_QTN),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc qtn_monitor
	@cd $(INSTALLDIR)/sbin && ln -sf rc start_psta_qtn
	@cd $(INSTALLDIR)/sbin && ln -sf rc start_ap_qtn
	@cd $(INSTALLDIR)/sbin && ln -sf rc start_nodfs_scan_qtn
	@cd $(INSTALLDIR)/sbin && ln -sf rc start_qtn_stateless
	@cd $(INSTALLDIR)/sbin && ln -sf rc restart_qtn
endif
ifeq ($(RTCONFIG_USB),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc usbled
endif
	@cd $(INSTALLDIR)/sbin && ln -sf rc ntp
	@cd $(INSTALLDIR)/sbin && ln -sf rc autodet
	@cd $(INSTALLDIR)/sbin && ln -sf rc ddns_updated
	@cd $(INSTALLDIR)/sbin && ln -sf rc ddns_custom_updated

#	@cd $(INSTALLDIR)/sbin && ln -sf rc wldist

ifeq ($(RTCONFIG_WPS),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc wpsaide
ifeq ($(RTCONFIG_QCA),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc get_wps_er
endif
endif

#	ifeq ($(RTCONFIG_HEARTBEAT),y)
#		@cd $(INSTALLDIR)/sbin && ln -sf rc hb_connect
#		@cd $(INSTALLDIR)/sbin && ln -sf rc hb_disconnect
#		@cd $(INSTALLDIR)/sbin && ln -sf rc hb_idle
#	endif

ifeq ($(RTCONFIG_CIFS),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc mount-cifs
endif

ifeq ($(RTCONFIG_RALINK),y)
ifeq ($(RTCONFIG_DSL),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc convert_dsl_wan_settings
endif
	@cd $(INSTALLDIR)/sbin && ln -sf rc rtkswitch
endif

ifeq ($(RTCONFIG_RTL8365MB),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc rtkswitch
endif

ifeq ($(RTCONFIG_QCA),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc rtkswitch
	@cd $(INSTALLDIR)/sbin && ln -sf rc delay_exec
endif

	@cd $(INSTALLDIR)/sbin && ln -sf rc wanduck
	@cd $(INSTALLDIR)/sbin && ln -sf rc tcpcheck
	@cd $(INSTALLDIR)/sbin && ln -sf rc auto_macclone
ifeq ($(RTCONFIG_USB),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc get_apps_name
	@cd $(INSTALLDIR)/sbin && ln -sf rc asus_sd
	@cd $(INSTALLDIR)/sbin && ln -sf rc asus_mmc
	@cd $(INSTALLDIR)/sbin && ln -sf rc asus_lp
	@cd $(INSTALLDIR)/sbin && ln -sf rc asus_sg
	@cd $(INSTALLDIR)/sbin && ln -sf rc asus_sr
	@cd $(INSTALLDIR)/sbin && ln -sf rc asus_tty
	@cd $(INSTALLDIR)/sbin && ln -sf rc asus_usbbcm
	@cd $(INSTALLDIR)/sbin && ln -sf rc asus_usb_interface
	@cd $(INSTALLDIR)/sbin && ln -sf rc usb_notify
	@cd $(INSTALLDIR)/sbin && ln -sf rc ejusb
ifeq ($(RTCONFIG_DISK_MONITOR),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc disk_monitor
endif
	@cd $(INSTALLDIR)/sbin && ln -sf rc disk_remove
ifeq ($(RTCONFIG_APP_PREINSTALLED)$(RTCONFIG_APP_NETINSTALLED),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc run_app_script
	@cd $(INSTALLDIR)/sbin && ln -sf rc chk_app_state
endif
endif
	@cd $(INSTALLDIR)/sbin && ln -sf rc restart_wireless
ifeq ($(RTCONFIG_BCMARM),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc nvram_erase
endif
	@cd $(INSTALLDIR)/sbin && ln -sf rc ATE
ifeq ($(or $(RTCONFIG_RALINK),$(RTCONFIG_QCA)),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc FWRITE
	@cd $(INSTALLDIR)/sbin && ln -sf rc FREAD
endif
ifeq ($(RTCONFIG_RALINK),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc asuscfe_2g
	@cd $(INSTALLDIR)/sbin && ln -sf rc asuscfe_5g
	@cd $(INSTALLDIR)/sbin && ln -sf rc stainfo_2g
	@cd $(INSTALLDIR)/sbin && ln -sf rc stainfo_5g
ifeq ($(RTCONFIG_DSL),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc gen_ralink_config
endif
endif
ifeq ($(RTCONFIG_QCA),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc FWRITE
	@cd $(INSTALLDIR)/sbin && ln -sf rc FREAD
endif
	@cd $(INSTALLDIR)/sbin && ln -sf rc run_telnetd 
ifeq ($(RTCONFIG_SSH),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc run_sshd
endif
	@cd $(INSTALLDIR)/sbin && ln -sf rc run_pptpd

ifeq ($(RTCONFIG_PARENTALCTRL),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc pc
	@cd $(INSTALLDIR)/sbin && ln -sf rc pc_block
endif
	@cd $(INSTALLDIR)/sbin && ln -sf rc wlcscan
	@cd $(INSTALLDIR)/sbin && ln -sf rc wlcconnect
ifeq ($(RTAC66U), y)
#	@cd $(INSTALLDIR)/sbin && ln -sf rc acsd_restart_wl
endif
	@cd $(INSTALLDIR)/sbin && ln -sf rc setup_dnsmq
	@cd $(INSTALLDIR)/sbin && ln -sf rc add_multi_routes

	@cd $(INSTALLDIR)/sbin && ln -sf rc free_caches
	@cd $(INSTALLDIR)/sbin && ln -sf rc get_phy_status
	@cd $(INSTALLDIR)/sbin && ln -sf rc get_phy_speed
	@cd $(INSTALLDIR)/sbin && ln -sf rc firmware_check
ifeq ($(BUILD_READMEM),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc readmem
endif
ifeq ($(RTCONFIG_HTTPS),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc rsasign_check
endif
	@cd $(INSTALLDIR)/sbin && ln -sf rc test_endian
ifeq ($(RTCONFIG_BWDPI),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc rsasign_sig_check
	@cd $(INSTALLDIR)/sbin && ln -sf rc bwdpi
	@cd $(INSTALLDIR)/sbin && ln -sf rc bwdpi_monitor
	@cd $(INSTALLDIR)/sbin && ln -sf rc bwdpi_check
	@cd $(INSTALLDIR)/sbin && ln -sf rc bwdpi_wred_alive
endif
ifeq ($(RTCONFIG_SPEEDTEST),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc speedtest
endif
ifeq ($(RTCONFIG_USB_MODEM),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc write_3g_ppp_conf
	@cd $(INSTALLDIR)/sbin && ln -sf rc modem_bytes_plus
endif
ifeq ($(RTCONFIG_IPERF),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc monitor
endif
ifeq ($(RT4GAC55U),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc lteled
endif
ifeq ($(and $(RTCONFIG_IPV6),$(RTCONFIG_WIDEDHCP6)),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc stop_ipv6
	@cd $(INSTALLDIR)/sbin && ln -sf rc start_dhcp6c
endif
ifeq ($(RTCONFIG_TR069),y)
	@cd $(INSTALLDIR)/sbin && ln -sf rc dhcpc_lease
endif

clean:
	rm -f rc *.o .*.depend

size: rc
	mipsel-uclibc-nm --print-size --size-sort rc

# handle prebuilt object here
ifneq ($(wildcard ./prebuild),)
ifeq ($(RTCONFIG_DSL),y)
ifeq ($(RTCONFIG_DSL_TCLINUX),y)
dsl_fb.o:
	@-cp -f ./prebuild/dsl_fb.o .
dsl_diag.o:
	@-cp -f ./prebuild/dsl_diag.o .
endif
endif
ifeq ($(RTCONFIG_HTTPS),y)
pwdec.o:
	@-cp -f ./prebuild/pwdec$(BCMEX).o pwdec.o
endif
else
ifeq ($(RTCONFIG_HTTPS),y)
dsl_fb.o:dsl_fb.c
	@echo "encrypt dsl password"
	./dsl_fb_enc.sh
	$(CC) $(CFLAGS) -c dsl_fb.c
	@echo "decrypt dsl password"
	./dsl_fb_dec.sh
endif
endif

%.o: %.c
	@echo " [rc] CC $@"
	@$(CC) $(CFLAGS) -c $<

.%.depend: %.c
	@$(CC) $(CFLAGS) -M $< > $@



pswatch: pswatch.c
	$(CC) -o $@ $^
	$(STRIP) $@
	$(CPTMP)
