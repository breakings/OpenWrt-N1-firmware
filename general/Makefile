#
# Copyright (C) 2017-2019 Entware
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# ported to Entware from
# https://github.com/KryptonLee/qBittorrent-openwrt-package and
# https://github.com/pawelkn/qt5-openwrt-package

include $(TOPDIR)/rules.mk

QT5_VERSION_MAJOR_MINOR:=5.15
QT5_VERSION_PATCH:=2

PKG_NAME:=qt5
PKG_VERSION:=$(QT5_VERSION_MAJOR_MINOR)$(if $(QT5_VERSION_PATCH),.$(QT5_VERSION_PATCH))
PKG_RELEASE:=1

PKG_SOURCE:=qt-everywhere-src-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=https://mirrors.tencent.com/qt/archive/qt/$(QT5_VERSION_MAJOR_MINOR)/$(PKG_VERSION)/single/ \
		https://mirrors.tuna.tsinghua.edu.cn/qt/archive/qt/$(QT5_VERSION_MAJOR_MINOR)/$(PKG_VERSION)/single/ \
		https://mirrors.ustc.edu.cn/qtproject/archive/qt/$(QT5_VERSION_MAJOR_MINOR)/$(PKG_VERSION)/single/ \
		http://download.qt-project.org/archive/qt/$(QT5_VERSION_MAJOR_MINOR)/$(PKG_VERSION)/single/
PKG_HASH:=3a530d1b243b5dec00bc54937455471aaa3e56849d2593edb8ded07228202240

PKG_BUILD_DIR=$(BUILD_DIR)/qt-everywhere-src-$(PKG_VERSION)

PKG_BUILD_DEPENDS:=libdouble-conversion pcre2 zlib
PKG_BUILD_PARALLEL:=1
PKG_USE_MIPS16:=0

include $(INCLUDE_DIR)/package.mk

ifndef CONFIG_USE_GLIBC
# not using sstrip here as this fucks up the .so's somehow
STRIP:=/bin/true
RSTRIP:= \
	NM="$(TOOLCHAIN_DIR)/bin/$(TARGET_CROSS)nm" \
	STRIP="$(STRIP)" \
	STRIP_KMOD="$(STRIP)" \
	$(SCRIPT_DIR)/rstrip.sh
endif

define Package/qt5/Default
	SECTION:=libs
	CATEGORY:=Libraries
	SUBMENU:=Qt5
	TITLE:=qt5
	URL:=http://qt-project.org
	DEPENDS:=+librt +libstdcpp +libpthread
endef

define Package/qt5-core
	$(call Package/qt5/Default)
	TITLE+=core
	DEPENDS+=+libatomic +libzstd +zlib +libpcre2-16 +libdouble-conversion +icu
endef

define Package/qt5-network
	$(call Package/qt5/Default)
	TITLE+=network
	DEPENDS+=+qt5-core +libopenssl
endef

define Package/qt5-sql
	$(call Package/qt5/Default)
	TITLE+=sql
	DEPENDS+=+qt5-core
endef

define Package/qt5-xml
	$(call Package/qt5/Default)
	TITLE+=xml
	DEPENDS+=+qt5-core
endef

CONFIGURE_ARGS = \
		-prefix /usr \
		-extprefix $(TOOLCHAIN_DIR) \
		-sysroot $(STAGING_DIR) \
		-plugindir /usr/lib/Qt/plugins \
		-R /usr/lib \
		-I $(STAGING_DIR)/usr/include \
		-L $(STAGING_DIR)/usr/lib \
		-xplatform linux-openwrt-g++ \
		-opensource \
		-confirm-license \
		-system-doubleconversion \
		-system-pcre \
		-system-zlib \
		-openssl-runtime \
		
		
		-no-cups \
		-no-freetype \
		-no-harfbuzz \
		-no-gtk \
		-no-opengl \
		-no-opengles3 \
		-no-angle \
		-no-openvg \
		-no-dbus \
		-no-directfb \
		-no-eglfs \
		-no-feature-getentropy \
		-no-iconv \
		-no-kms \
		-no-pch \
		-no-libudev \
		-no-mtdev \
		-no-tslib \
		-no-libpng \
		-no-libjpeg \
		-no-sql-db2 \
		-no-sql-ibase \
		-no-sql-mysql \
		-no-sql-oci \
		-no-sql-odbc \
		-no-sql-psql \
		-no-sql-sqlite \
		-no-sql-sqlite2 \
		-no-sqlite \
		-no-sql-tds \
		-no-strip \
		-no-xcb \
		-no-gui \
		-no-widgets \
		-no-glib \
		-make libs \
		-nomake examples \
		-nomake tests \
		-nomake tools \
		-skip 3d \
		-skip activeqt \
		-skip charts \
		-skip connectivity \
		-skip datavis3d \
		-skip declarative \
		-skip doc \
		-skip gamepad \
		-skip imageformats \
		-skip location \
		-skip networkauth \
		-skip multimedia \
		-skip purchasing \
		-skip remoteobjects \
		-skip script \
		-skip sensors \
		-skip serialbus \
		-skip serialport \
		-skip speech \
		-skip svg \
		-skip wayland \
		-skip webengine \
		-skip websockets \
		-skip webchannel \
		-skip xmlpatterns \
		-v \

define Build/Configure
	$(SED) 's@$$$$(TARGET_CROSS)@$(TARGET_CROSS)@g' $(PKG_BUILD_DIR)/qtbase/mkspecs/linux-openwrt-g++/qmake.conf
	$(SED) 's@$$$$(TARGET_CFLAGS)@$(TARGET_CFLAGS)@g' $(PKG_BUILD_DIR)/qtbase/mkspecs/linux-openwrt-g++/qmake.conf
	$(SED) 's@$$$$(TARGET_CXXFLAGS)@$(TARGET_CXXFLAGS)@g' $(PKG_BUILD_DIR)/qtbase/mkspecs/linux-openwrt-g++/qmake.conf
	$(SED) 's@$$$$(TARGET_LDFLAGS)@$(TARGET_LDFLAGS)@g' $(PKG_BUILD_DIR)/qtbase/mkspecs/linux-openwrt-g++/qmake.conf

	$(call Build/Configure/Default)
endef

define Build/Compile
	$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR)
endef

define Build/InstallDev
	$(MAKE) -C $(PKG_BUILD_DIR) install
	$(INSTALL_CONF) $(PKG_BUILD_DIR)/qtbase/bin/qt.conf $(STAGING_DIR)/usr/bin
	$(RM) $(STAGING_DIR)/usr/bin/qt{attributionsscanner,paths,plugininfo}
endef

define Package/qt5-core/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt5Core.so* $(1)/usr/lib
endef

define Package/qt5-network/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt5Network.so* $(1)/usr/lib
endef

define Package/qt5-sql/install
	$(INSTALL_DIR) $(1)/usr/lib/
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt5Sql.so* $(1)/usr/lib/
endef

define Package/qt5-xml/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_BUILD_DIR)/qtbase/lib/libQt5Xml.so* $(1)/usr/lib
endef

$(eval $(call BuildPackage,qt5-core,+zlib,+libpcre2-16,+libdouble-conversion))
$(eval $(call BuildPackage,qt5-network))
$(eval $(call BuildPackage,qt5-sql))
$(eval $(call BuildPackage,qt5-xml))
