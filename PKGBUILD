# Maintainer: Doug Newgard <scimmia22 at outlook dot com>
# Contributor: Ronald van Haren <ronald.archlinux.org>

pkgname=enlightenment17-svn
pkgver=82125
pkgrel=1
pkgdesc="Enlightenment window manager DR17 (aka e17)"
arch=('i686' 'x86_64')
url="http://www.enlightenment.org"
license=('BSD')
depends=('elementary-svn' 'xcb-util-keysyms' 'hicolor-icon-theme'
         'desktop-file-utils' 'udisks' 'ttf-font')
makedepends=('subversion')
optdepends=('connman: network module')
conflicts=('e' 'e-svn' 'enlightenment17')
provides=('e' 'e-svn' 'enlightenment17' 'notification-daemon')
backup=('etc/enlightenment/sysactions.conf')
options=('!libtool' '!strip')
install=enlightenment17.install

_svntrunk="http://svn.enlightenment.org/svn/e/trunk/e"
_svnmod="e"

build() {
  cd "$srcdir"

  msg "Connecting to SVN server...."

  if [[ -d "$_svnmod/.svn" ]]; then
    (cd "$_svnmod" && svn up -r "$pkgver")
  else
    svn co "$_svntrunk" --config-dir ./ -r "$pkgver" "$_svnmod"
  fi

  msg "SVN checkout done or server timeout"
  msg "Starting build..."

  rm -rf "$srcdir/$_svnmod-build"
  svn export "$srcdir/$_svnmod" "$srcdir/$_svnmod-build"
  cd "$srcdir/$_svnmod-build"
  
  # set suspend and hibernate to use systemd
  sed -i -e 's:@SUSPEND@:/usr/bin/systemctl suspend:g' \
         -e 's:@HIBERNATE@:/usr/bin/systemctl hibernate:g' \
         data/etc/sysactions.conf.in

  ./autogen.sh --prefix=/usr \
	--sysconfdir=/etc

  make
}
         
package() {
  cd "$srcdir/$_svnmod-build"

  make DESTDIR="$pkgdir" install

# install license files
  install -Dm644 "$srcdir/$_svnmod-build/COPYING" \
	"$pkgdir/usr/share/licenses/$pkgname/COPYING"

  install -Dm644 "$srcdir/$_svnmod-build/AUTHORS" \
	"$pkgdir/usr/share/licenses/$pkgname/AUTHORS"

  rm -r "$srcdir/$_svnmod-build"
}
