# Maintainer: Xavier Devlamynck <magicrhesus@ouranos.be>
# Contributor: Clement Demoulins <clement@archivel.fr>
# Contributor: Alexander Baldeck <alexander@archlinux.org>
# Contributor: Dale Blount <dale@archlinux.org>
# Contributor: Anders Bostrom <anders.bostrom@home.se>
pkgname=thunderbird-ldap
pkgver=3.1.10
pkgrel=2
pkgdesc="Standalone Mail/News reader"
arch=('i686' 'x86_64')
license=('MPL' 'GPL')
url="http://www.mozilla.org/projects/thunderbird"
depends=('gtk2' 'gcc-libs' 'mozilla-common' 'nss' 'libxt' 'shared-mime-info' 'alsa-lib' 'dbus-glib' 'hunspell' 'sqlite3>=3.7.4' 'desktop-file-utils' 'libnotify')
makedepends=('zip' 'pkg-config' 'python2' 'libidl2' 'wireless_tools' 'autoconf2.13')
optdepends=('libcanberra: for sound support')
install=thunderbird.install
source=(ftp://ftp.mozilla.org/pub/mozilla.org/thunderbird/releases/${pkgver}/source/thunderbird-${pkgver}.source.tar.bz2
        mozconfig
        thunderbird.desktop
        thunderbird-3.0-lang.patch
        thunderbird-appversion.patch
        thunderbird-shared-error.patch
        xulrunner-png14.patch
        python2.7.patch
	gcc46.patch
	experimental_writeable_ldap.patch)
md5sums=('2a81f3ee7ea7acff0ee2eaee6574ea11'
         '529d4caad3027116ab9e000438ca70fa'
         '37457073c7f6feeb86c053701d23197f'
         '25b6fe16ac24cd5c852213e5c1adb272'
         '48ffcdb877a69d383b7d354e330f7658'
         '3dca714ee8054634e6dfdb059464dc42'
         '989b15f6bc9e2e9233fe4c6b23f412b1'
         '78a4d0c920cbb8ec6a9a36974cd775ef'
         'a2bced1791fb8eca978d31b129f92a28'
	 '7422528ec26a578203d6f3276c64c713')

build() {
  cd "${srcdir}/comm-1.9.2"
  patch -Np1 -i "${srcdir}/thunderbird-3.0-lang.patch"
  patch -Np1 -i "${srcdir}/thunderbird-appversion.patch"
  patch -Np1 -i "${srcdir}/thunderbird-shared-error.patch"
  patch -Np1 -i "${srcdir}/gcc46.patch"
  patch -Np0 -i "${srcdir}/xulrunner-png14.patch"
  patch -Np0 -i "${srcdir}/python2.7.patch"
  patch -Np0 -i "${srcdir}/experimental_writeable_ldap.patch" "${srcdir}/comm-1.9.2/mailnews/addrbook/src/nsAbLDAPDirectory.cpp"

  cp "${srcdir}/mozconfig" .mozconfig
  unset CXXFLAGS
  unset CFLAGS

  export CXXFLAGS="-fpermissive"
  export LDFLAGS="-Wl,-rpath,/usr/lib/thunderbird-3.1"

  make -j1 -f client.mk build MOZ_MAKE_FLAGS="${MAKEFLAGS}"
}

package() {
  cd "${srcdir}/comm-1.9.2"
  make -j1 DESTDIR="${pkgdir}" install

  #Remove included dictionaries, add symlink to system myspell path.
  #Note: this will cause file conflicts when users have installed dictionaries in the old location
  rm -rf "${pkgdir}/usr/lib/thunderbird-3.1/dictionaries"
  ln -sf /usr/share/myspell/dicts "${pkgdir}/usr/lib/thunderbird-3.1/dictionaries"

  rm -rf "${pkgdir}/usr/bin/defaults"

  install -m755 -d "${pkgdir}/usr/share/applications"

  for i in 16x16 22x22 24x24 32x32 48x48 256x256; do
      install -d "$pkgdir/usr/share/icons/hicolor/$i/apps"
      cp other-licenses/branding/thunderbird/mailicon${i/x*/}.png "$pkgdir/usr/share/icons/hicolor/$i/apps/thunderbird.png"
  done

  install -m644 "${srcdir}/thunderbird.desktop" "${pkgdir}/usr/share/applications/"

  rm -f ${pkgdir}/usr/lib/pkgconfig/thunderbird-ns{s,pr}.pc
}
