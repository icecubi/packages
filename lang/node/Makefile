# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=node

ifeq ($(CONFIG_NODEJS_22),y)
PKG_VERSION:=v22.13.1
PKG_RELEASE:=1
PKG_HASH:=cfce282119390f7e0c2220410924428e90dadcb2df1744c0c4a0e7baae387cc2
PATCH_DIR:=./patches/v22.x
else
ifeq ($(CONFIG_NODEJS_20),y)
PKG_VERSION:=v20.18.2
PKG_RELEASE:=1
PKG_HASH:=69bf81b70f3a95ae0763459f02860c282d7e3a47567c8afaf126cc778176a882
PATCH_DIR:=./patches/v20.x
else
ifeq ($(CONFIG_NODEJS_18),y)
PKG_VERSION:=v18.15.0
PKG_RELEASE:=1
PKG_HASH:=8e44d65018ff973284195c23186469a0ea4082e97ec4200e5f5706d5584daa37
PATCH_DIR:=./patches/v18.x
else
ifeq ($(CONFIG_NODEJS_16),y)
PKG_VERSION:=v16.19.1
PKG_RELEASE:=1
PKG_HASH:=17fb716406198125b30c94dd3d1756207b297705626afe16d8dc479a65a1d8b5
PATCH_DIR:=./patches/v16.x
else
ifeq ($(CONFIG_NODEJS_14),y)
PKG_VERSION:=v14.21.3
PKG_RELEASE:=1
PKG_HASH:=458ec092e60ad700ddcf079cb63d435c15da4c7bb3d3f99b9a8e58a99e54075e
PATCH_DIR:=./patches/v14.x
else
PKG_VERSION:=v12.22.12
PKG_RELEASE:=2
PKG_HASH:=bc42b7f8495b9bfc7f7850dd180bb02a5bdf139cc232b8c6f02a6967e20714f2
PATCH_DIR:=./patches/v12.x
endif
endif
endif
endif
endif

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=https://nodejs.org/dist/$(PKG_VERSION)

PKG_MAINTAINER:=Hirokazu MORIKAWA <morikw2@gmail.com>
PKG_LICENSE:=MIT
PKG_LICENSE_FILES:=LICENSE
PKG_CPE_ID:=cpe:/a:nodejs:node.js

HOST_BUILD_DEPENDS:=python3/host
HOST_BUILD_PARALLEL:=1

PKG_BUILD_DEPENDS:=python3/host
PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1
PKG_USE_MIPS16:=0
PKG_ASLR_PIE:=0

include $(INCLUDE_DIR)/host-build.mk
include $(INCLUDE_DIR)/package.mk

define Package/node
  SECTION:=lang
  CATEGORY:=Languages
  SUBMENU:=Node.js
  TITLE:=Node.js is a platform built on Chrome's JavaScript runtime
  URL:=https://nodejs.org/
  DEPENDS:=@(HAS_FPU||KERNEL_MIPS_FPU_EMULATOR||SOFT_FLOAT) @!arc @!armeb @!powerpc \
	   +libstdcpp +(NODEJS_18||NODEJS_20||NODEJS_22):libopenssl +zlib +libnghttp2 +libuv \
	   +libcares +libatomic +NODEJS_ICU_SYSTEM:icu +NODEJS_ICU_SYSTEM:icu-full-data
endef

define Package/node/description
  Node.js® is a JavaScript runtime built on Chrome's V8 JavaScript engine. Node.js uses
  an event-driven, non-blocking I/O model that makes it lightweight and efficient. Node.js'
   package ecosystem, npm, is the largest ecosystem of open source libraries in the world.

  *** The following preparations must be made on the host side. ***
      1. gcc 8.3 or higher is required.
      2. To build a 32-bit target, gcc-multilib, g++-multilib are required.
      3. Requires libatomic package. (If necessary, install the 32-bit library at the same time.)
     ex) sudo apt-get install gcc-multilib g++-multilib
endef

define Package/node-npm
  SECTION:=lang
  CATEGORY:=Languages
  SUBMENU:=Node.js
  TITLE:=NPM stands for Node Package Manager
  URL:=https://www.npmjs.com/
  DEPENDS:=+node
endef

define Package/node-npm/description
	NPM is the package manager for NodeJS
endef

define Package/node/config
	menu "Configuration"

	choice
		prompt "Version Selection"
		default NODEJS_12 if mips||mipsel||mips64||mips64el
		default NODEJS_20
		help
		 Select node.js version.
		 The host package version is also the same.

		config NODEJS_12
			bool "12.x End-of-life : Only for MIPS"

		config NODEJS_14
			depends on !(mips||mipsel||mips64||mips64el)
			bool "14.x End-of-Life"

		config NODEJS_16
			depends on !(mips||mipsel||mips64||mips64el)
			bool "16.x End-of-Life"

		config NODEJS_18
			depends on !(mips||mipsel||mips64||mips64el)
			bool "18.x Maintenance LTS"
			
		config NODEJS_20
			depends on !(mips||mipsel||mips64||mips64el)
			bool "20.x Active LTS"
			
		config NODEJS_22
			depends on !(mips||mipsel||mips64||mips64el)
			bool "22.x Active LTS"
	endchoice

	if PACKAGE_node
		choice
			prompt "i18n features"
			default NODEJS_ICU_SMALL
			help
			 Select i18n features

			config NODEJS_ICU_NONE
				bool "Disable"

			config NODEJS_ICU_SMALL
				depends on !BIG_ENDIAN
				bool "small-icu"

			config NODEJS_ICU_SYSTEM
				depends on ARCH_64BIT&&!BIG_ENDIAN
				bool "system-icu"
		endchoice
	endif

	endmenu
endef

NODEJS_CPU:=$(subst powerpc,ppc,$(subst aarch64,arm64,$(subst x86_64,x64,$(subst i386,ia32,$(ARCH)))))

ifeq ($(CONFIG_NODEJS_10),y)
TARGET_LDFLAGS+=-latomic
endif

ifneq ($(CONFIG_ARCH_64BIT),y)
FORCE_32BIT:=-m32
endif

MAKE_VARS+= \
	DESTCPU=$(NODEJS_CPU) \
	NO_LOAD='cctest.target.mk embedtest.target.mk node_mksnapshot.target.mk overlapped-checker.target.mk \
		mkcodecache.target.mk tools/v8_gypfiles/torque_base.target.mk tools/v8_gypfiles/v8_init.target.mk' \
	LD_LIBRARY_PATH=$(STAGING_DIR_HOSTPKG)/share/icu/current/lib

HOST_MAKE_VARS+=NO_LOAD='cctest.target.mk embedtest.target.mk overlapped-checker.target.mk'

CONFIGURE_VARS:= \
	CC="$(TARGET_CC) $(TARGET_OPTIMIZATION)" \
	CXX="$(TARGET_CXX) $(TARGET_OPTIMIZATION)" \
	CC_host="$(HOSTCC) $(FORCE_32BIT)" \
	CXX_host="$(HOSTCXX) $(FORCE_32BIT)"

CONFIGURE_ARGS:= \
	--dest-cpu=$(NODEJS_CPU) \
	--dest-os=linux \
	--cross-compiling \
	--shared-zlib \
	--shared-openssl \
	--shared-nghttp2 \
	--shared-cares \
	--with-intl=$(if $(CONFIG_NODEJS_ICU_SMALL),small-icu,$(if $(CONFIG_NODEJS_ICU_SYSTEM),system-icu,none)) \
	$(if $(findstring mips,$(NODEJS_CPU)), \
		$(if $(CONFIG_SOFT_FLOAT),--with-mips-float-abi=soft)) \
	$(if $(findstring +neon,$(CONFIG_CPU_TYPE)),--with-arm-fpu=neon) \
	$(if $(findstring +vfp",$(CONFIG_CPU_TYPE)),--with-arm-fpu=vfp) \
	$(if $(findstring +vfpv3",$(CONFIG_CPU_TYPE)),--with-arm-fpu=vfpv3-d16) \
	$(if $(findstring +vfpv4",$(CONFIG_CPU_TYPE)),--with-arm-fpu=vfpv3) \
	--prefix=/usr

HOST_CONFIGURE_VARS:=
HOST_CONFIGURE_ARGS:= \
	--dest-os=$(if $(findstring Darwin,$(HOST_OS)),mac,linux) \
	--with-intl=small-icu \
	--prefix=$(STAGING_DIR_HOSTPKG)

define Build/InstallDev
	$(INSTALL_DIR) $(1)/usr/include
	$(CP) $(PKG_INSTALL_DIR)/usr/include/* $(1)/usr/include/
endef

define Package/node/install
	$(INSTALL_DIR) $(1)/usr/bin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/usr/bin/node $(1)/usr/bin/
endef

define Package/node-npm/install
	$(INSTALL_DIR) $(1)/usr/lib/node_modules/npm
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/npm/{package.json,LICENSE} \
		$(1)/usr/lib/node_modules/npm/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/npm/README.md \
		$(1)/usr/lib/node_modules/npm/
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/node_modules/npm/{node_modules,bin,lib} \
		$(1)/usr/lib/node_modules/npm/
	$(INSTALL_DIR) $(1)/usr/bin
	$(LN) ../lib/node_modules/npm/bin/npm-cli.js $(1)/usr/bin/npm
	$(LN) ../lib/node_modules/npm/bin/npx-cli.js $(1)/usr/bin/npx
endef

define Host/Install
	$(RM) -rf $(1)/lib/node_modules/npm
	$(call Host/Install/Default)
endef

define Package/node-npm/postrm
#!/bin/sh
rm -rf /usr/lib/node_modules/npm || true
endef

$(eval $(call HostBuild))
$(eval $(call BuildPackage,node))
$(eval $(call BuildPackage,node-npm))
