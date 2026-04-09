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

GO_PKG:=github.com/yichya/xray-geodata-cut

include $(INCLUDE_DIR)/package.mk

define Package/$(PKG_NAME)
	SECTION:=Custom
	CATEGORY:=Extra packages
	TITLE:=openwrt-xray-geodata-cut
	PROVIDES:=xray-geodata
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

endmenu
endef

# ---------- VARS ----------

PROXYCHAINS:=
ifdef CONFIG_PACKAGE_OPENWRT_XRAY_GEODATA_CUT_FETCH_VIA_PROXYCHAINS
	PROXYCHAINS:=proxychains
endif

# ---------- BUILD ----------

define Build/Compile
	$(INSTALL_DIR) $(PKG_INSTALL_DIR)/bin

	cd $(PKG_BUILD_DIR); \
	$(GO_HOST_PKG_VARS) \
	go mod download; \
	go mod edit -replace github.com/sagernet/sing=github.com/sagernet/sing@v0.3.3; \
	CGO_ENABLED=0 \
	go build -trimpath -ldflags "-s -w" \
	-o $(PKG_INSTALL_DIR)/bin/xray-geodata-cut .
endef

# ---------- INSTALL ----------

define Package/$(PKG_NAME)/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/bin/xray-geodata-cut $(1)/usr/bin/

	$(INSTALL_DIR) $(1)/usr/share/xray

	$(PROXYCHAINS) wget -q https://github.com/Loyalsoldier/v2ray-rules-dat/releases/latest/download/geoip.dat -O $(1)/usr/share/xray/geoip.dat
	$(PROXYCHAINS) wget -q https://github.com/Loyalsoldier/v2ray-rules-dat/releases/latest/download/geosite.dat -O $(1)/usr/share/xray/geosite.dat
endef

$(eval $(call BuildPackage,$(PKG_NAME)))
