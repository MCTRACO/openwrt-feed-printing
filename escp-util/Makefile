#
# Copyright (C) 2013 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=escp-util
PKG_VERSION:=5.3.4
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=@SF/gimp-print
PKG_HASH:=1734950dacc59d52036aa9f5da64dad865f1d247bb3875b09e7d6d2e21090b68


PKG_INSTALL:=1
PKG_FIXUP=autoreconf

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

define Package/escp-util
  SECTION:=net
  CATEGORY:=Network
  TITLE:=escp-util -- Epson Inkjet tool
  DEPENDS:=
  URL:=http://gimp-print.sourceforge.net/
  SUBMENU:=Printing
endef


define Package/escp-util/description
	Epson inkjet tool made from gutenprint
endef

CONFIGURE_ARGS+= \
	--disable-nls \
	--disable-test \
	--without-doc \
	--without-readline \
	--without-foomatic \
	--without-foomatic3 \
	--enable-escputil \
	--without-gimp2 \     
	--without-cups \
	--disable-translated-cups-ppds \
	--disable-globalized-cups-ppds \
	--disable-simplified-cups-ppds \
	--bindir=/usr/bin \
	--sbindir=/usr/bin \
	--prefix=/usr \
	--exec-prefix=/usr

# Patch the Makefiles to run the binary from host compile
define Build/Compile
	$(SED) "s#\.\(/extract-strings\)#$(HOST_BUILD_DIR)/src/xml\1#" $(PKG_BUILD_DIR)/src/xml/Makefile
	$(SED) "s#\.\(/check_duplicate_printers.test\)#$(HOST_BUILD_DIR)/src/xml/printers\1#" $(PKG_BUILD_DIR)/src/xml/printers/Makefile
	$(call Build/Compile/Default)
endef

define Package/escp-util/install
	$(INSTALL_DIR) $(1)/usr/sbin $(1)/usr/bin
	# XXX Do not install cups-genppdupdate, as it requires Perl + some perl libs
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/sbin/cups-genppd.[0-9]*.[0-9]* $(1)/usr/sbin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/cups-calibrate $(1)/usr/bin/

	$(INSTALL_DIR) $(1)/usr/lib/cups/driver $(1)/usr/lib/cups/filter
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/cups/driver/gutenprint.* $(1)/usr/lib/cups/driver
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/lib/cups/filter/* $(1)/usr/lib/cups/filter
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/lib/libgutenprint.* $(1)/usr/lib
	$(INSTALL_DIR) $(1)/usr/share/cups/mime
	$(CP) $(PKG_INSTALL_DIR)/etc/cups/command.types $(1)/usr/share/cups/mime
	$(INSTALL_DIR) $(1)/usr/share/gutenprint
	$(CP) $(PKG_INSTALL_DIR)/usr/share/gutenprint/[0-9]*.[0-9]* $(1)/usr/share/gutenprint
endef

define Host/Compile
	$(MAKE) $(HOST_JOBS) -C $(HOST_BUILD_DIR)/src/xml $(HOST_MAKE_FLAGS) extract-strings
	$(MAKE) $(HOST_JOBS) -C $(HOST_BUILD_DIR)/src/xml/printers $(HOST_MAKE_FLAGS) check_duplicate_printers.test
endef

define Host/Install
endef

$(eval $(call HostBuild))
$(eval $(call BuildPackage,escp-util))
