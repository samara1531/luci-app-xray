include $(TOPDIR)/rules.mk

PKG_NAME:=openwrt-xray-geodata-cut
PKG_VERSION:=3.7.1
PKG_RELEASE:=1

PKG_LICENSE:=MPLv2
PKG_LICENSE_FILES:=LICENSE
PKG_MAINTAINER:=yichya <mail@yichya.dev>

PKG_SOURCE_PROTO:=git
PKG_SOURCE_URL:=https://github.com/yichya/xray-geodata-cut.git
PKG_SOURCE_VERSION:=86a309fc432f7c6caeb66b6785fae727f6be6ced
PKG_MIRROR_HASH:=skip

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_FIXUP:=golang

GO_PKG:=github.com/yichya/xray-geodata-cut
GO_PKG_BUILD_PKG:=$(GO_PKG)

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/../feeds/packages/lang/golang/golang-package.mk

define Package/$(PKG_NAME)
	SECTION:=Custom
	CATEGORY:=Extra packages
	TITLE:=openwrt-xray-geodata-cut
	PROVIDES:=xray-geodata
	DEPENDS:=$(GO_ARCH_DEPENDS)
endef

define Package/$(PKG_NAME)/description
	Slim geodata files when compiling OpenWrt with Xray
endef

# ---------- CONFIG ----------

define Package/$(PKG_NAME)/config
menu "openwrt-xray-geodata-cut Configuration"
	depends on PACKAGE_$(PKG_NAME)

config PACKAGE_OPENWRT_XRAY_GEODATA_CUT_FETCH_VIA_PROXYCHAINS
	bool "Fetch via proxychains"
	default n

config PACKAGE_OPENWRT_XRAY_GEODATA_CUT_GEOSITE
	bool "Trim GeoSite"
	default n

config PACKAGE_OPENWRT_XRAY_GEODATA_CUT_GEOIP
	bool "Trim GeoIP"
	default n

endmenu
endef

# ---------- VARS ----------

PROXYCHAINS:=
ifdef CONFIG_PACKAGE_OPENWRT_XRAY_GEODATA_CUT_FETCH_VIA_PROXYCHAINS
	PROXYCHAINS:=proxychains
endif

# ---------- BUILD ----------

# используем стандартную сборку golang-package.mk
define Build/Compile
	$(call GoPackage/Build/Compile)
endef

# ---------- INSTALL ----------

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(GO_PKG_BUILD_DIR)/xray-geodata-cut $(1)/usr/bin/

	$(INSTALL_DIR) $(1)/usr/share/xray

	$(PROXYCHAINS) wget -q https://github.com/Loyalsoldier/v2ray-rules-dat/releases/latest/download/geoip.dat -O $(1)/usr/share/xray/geoip.dat
	$(PROXYCHAINS) wget -q https://github.com/Loyalsoldier/v2ray-rules-dat/releases/latest/download/geosite.dat -O $(1)/usr/share/xray/geosite.dat
endef

$(eval $(call BuildPackage,$(PKG_NAME)))
