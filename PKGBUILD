# Maintainer: Eric Vidal <eric@obarun.org>
# based on the original https://git.archlinux.org/svntogit/packages.git/log/trunk?h=packages/lighttpd
# 						Maintainer: Pierre Schmitz <pierre@archlinux.de>

pkgname=lighttpd
pkgver=1.4.48
pkgrel=2
pkgdesc='A secure, fast, compliant and very flexible web-server'
license=('custom')
arch=(x86_64)
url="http://www.lighttpd.net/"
depends=('pcre' 'util-linux')
makedepends=('libmariadbclient' 'lua' 'libxml2' 'e2fsprogs' 'sqlite' 'gdbm' 'pkgconfig')
optdepends=('libxml2: mod_webdav'
            'lua: mod_cml/mod_magnet'
            'libmariadbclient: mod_mysql_vhost/mod_authn_mysql'
            'sqlite: mod_webdav')
backup=('etc/lighttpd/lighttpd.conf' 'etc/logrotate.d/lighttpd')
options=('emptydirs')
source=("https://download.lighttpd.net/lighttpd/releases-1.4.x/lighttpd-${pkgver}.tar.xz"
        'lighttpd.logrotate.d' 'lighttpd.conf' 'lighttpd.tmpfiles')
sha256sums=('0f8ad5aac7529d7b948b9d7e8cd0b4a9e177309d85d6bf6516e28e6e40d74f36'
            '41f6c0042bb61021553779f861910e335834f6c15e4411756cdc6233b31076fe'
            'fece4581bebf39768571962dedce176b2b5f487c0abb5c1cfb35395de216c01f'
            'd8a185145a7c08b4fd8c8e6c12dae3e176389dd9b1c66e239757b2ba5108c871')
validpgpkeys=('6DD4217456569BA711566AC7F06E8FDE7B45DAAC') # Eric Vidal

build() {
	cd $srcdir/$pkgname-$pkgver

	./configure --prefix=/usr \
		--sbindir=/usr/bin \
		--libdir=/usr/lib/lighttpd/ \
		--sysconfdir=/etc/lighttpd \
		--with-mysql \
		--with-ldap \
		--with-attr \
		--with-openssl \
		--with-kerberos5 \
		--without-fam \
		--with-webdav-props \
		--with-webdav-locks \
		--with-gdbm \
		--with-lua
	sed -i -e 's/ -shared / -Wl,-O1,--as-needed\0/g' libtool
	make
}

check() {
	cd $srcdir/$pkgname-$pkgver
	make check
}

package() {
	cd $srcdir/$pkgname-$pkgver
	make DESTDIR=$pkgdir install

	install -D -m644 $srcdir/lighttpd.logrotate.d $pkgdir/etc/logrotate.d/lighttpd
	install -D -m644 $srcdir/lighttpd.conf $pkgdir/etc/lighttpd/lighttpd.conf
	install -d -m700 -o http -g http $pkgdir/var/{log,cache}/lighttpd/
	install -D -m644 ${srcdir}/lighttpd.tmpfiles ${pkgdir}/usr/lib/tmpfiles.d/lighttpd.conf
	

	pushd doc/config >/dev/null
	find . -type f ! -name 'Makefile*' -exec install -D -m644 {} ${pkgdir}/usr/share/doc/lighttpd/config/{} \;
	popd >/dev/null

	install -D -m644 COPYING $pkgdir/usr/share/licenses/$pkgname/COPYING
}
