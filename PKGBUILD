# Maintainer: Gerad Munsch <gmunsch@unforgivendevelopment.com>

## BUILT FROM 'network-ups-tools' PKGBUILD -- original maintainers:
# Maintainer: Alex Reznichenko <sa5gap@yandex.ru>
# Contributor: Roman Kyrylych <roman@archlinux.org>
# Contributor: Giuseppe Lucarelli <luk@rebelsoft.org>
# Contributor: Raphaël Doursenaud <rdoursenaud@free.fr>

pkgname=network-ups-tools-full
pkgver=2.6.5
pkgrel=6
pkgdesc="NUT is a collection of programs for monitoring and administering UPS hardware (with SNMP, development files, avahi, logrotate, and bash completion support)"
arch=('i686' 'x86_64')
url="http://www.networkupstools.org/"
license=('GPL2')
depends=('openssl' 'libusb-compat' 'net-snmp' 'desktop-file-utils')
optdepends=('avahi' 'logrotate' 'python2: for nut-monitor gui')
makedepends=('avahi' 'logrotate')
conflicts=('network-ups-tools')
replaces=('network-ups-tools')
provides=('network-ups-tools')
backup=(etc/ups/{ups.conf,upsd.conf,upsd.users,upsmon.conf,upssched.conf})
install=nut.install
source=('http://www.networkupstools.org/source/2.6/nut-$pkgver.tar.gz')
options=('!emptydirs' '!libtool')
md5sums=('e6eac4fa04baff0d0a827d64efe81a7e')

build() {

  cd $srcdir/nut-$pkgver

  # Nut-monitor to lowercase
  sed -i 's|=NUT-Monitor|=nut-monitor|' scripts/python/app/nut-monitor.desktop
  sed -i "s|sys.argv\[0\]|'/usr/share/nut/nut-monitor/nut-monitor'|" scripts/python/app/NUT-Monitor
  sed -i 's|/usr/bin/env python|/usr/bin/env python2.7|' scripts/python/app/NUT-Monitor

  ./configure \
    --without-wrap \
    --with-user=ups \
    --with-group=nut \
    --with-usb \
    --with-snmp \
    --with-dev \
    --with-avahi \
    --prefix=/usr \
    --with-udev-dir=/usr/lib/udev \
    --with-systemdsystemunitdir=/lib/systemd/system \
    --datadir=/usr/share/ups \
    --includedir=/usr/include/network-ups-tools \
    --sbindir=/usr/bin \
    --with-drvpath=/usr/bin \
    --with-pkgconfig-dir=/usr/lib/pkgconfig \
    --sysconfdir=/etc/ups || return 1

  make || return 1

}

package() {

  cd $srcdir/nut-$pkgver
  make DESTDIR=$pkgdir install || return 1

  install -D -m644 conf/ups.conf.sample $pkgdir/etc/ups/ups.conf
  install -D -m640 conf/upsd.conf.sample $pkgdir/etc/ups/upsd.conf
  install -D -m640 conf/upsd.users.sample $pkgdir/etc/ups/upsd.users
  install -D -m640 conf/upsmon.conf.sample $pkgdir/etc/ups/upsmon.conf
  install -D -m644 conf/upssched.conf.sample $pkgdir/etc/ups/upssched.conf
  install -D -m644 scripts/avahi/nut.service $pkgdir/etc/avahi/services/network-ups-tools.service
  install -D -m644 scripts/logrotate/nutlogd $pkgdir/etc/logrotate.d/network-ups-tools
  install -D -m755 scripts/misc/nut.bash_completion $pkgdir/etc/bash_completion.d/network-ups-tools

  # Nut-monitor GUI
  install -D -m644 scripts/python/module/PyNUT.py $pkgdir/usr/lib/python2.7/site-packages/PyNUT.py
  mkdir -p $pkgdir/usr/share/nut/nut-monitor/pixmaps
  install -m 755 scripts/python/app/NUT-Monitor $pkgdir/usr/share/nut/nut-monitor/nut-monitor
  install -m 644 scripts/python/app/gui-1.3.glade $pkgdir/usr/share/nut/nut-monitor
  install -m 644 scripts/python/app/pixmaps/* $pkgdir/usr/share/nut/nut-monitor/pixmaps/
  install -D scripts/python/app/nut-monitor.png $pkgdir/usr/share/pixmaps/nut-monitor.png
  desktop-file-install --dir=$pkgdir/usr/share/applications scripts/python/app/nut-monitor.desktop
  ln -s /usr/share/nut/nut-monitor/nut-monitor $pkgdir/usr/bin/nut-monitor

}
