# $Id: PKGBUILD 83715 2013-02-04 16:30:55Z spupykin $
# Maintainer: Sergej Pupykin <pupykin.s+arch@gmail.com>
# Maintainer: Ondrej Jirman <megous@megous.com>

pkgname=mingw32-gcc
pkgver=4.7.2
pkgrel=1
_uprel=1
arch=(i686 x86_64)
pkgdesc="A C and C++ cross-compilers for building Windows executables on Linux"
depends=('mingw32-pthreads' 'mingw32-runtime' 'mingw32-binutils' 'mingw32-w32api'
	 'libmpc' 'elfutils' 'gmp')
replaces=('mingw32-gcc-base')
provides=('mingw32-gcc-base')
options=(!strip)
url="http://sourceforge.net/projects/mingw/files/MinGW/Base/gcc/Version4/"
license=(GPL LGPL)
source=(gcc-$pkgver-${_uprel}-mingw32-src.tar.lzma::http://downloads.sourceforge.net/project/mingw/MinGW/Base/gcc/Version4/gcc-$pkgver-${_uprel}/gcc-$pkgver-${_uprel}-mingw32-src.tar.lzma
        gcc-1-mingw-float.patch)
md5sums=('bc2d1828dd6c53683600545ca6a901e1'
         '2407123c35c0aa92ee5ffc27decca9a7')

build() {
  [ $NOEXTRACT -eq 1 ] || tar xjf gcc-$pkgver-${_uprel}-mingw32-src/gcc-$pkgver.tar.bz2

  patch -d $srcdir/gcc-$pkgver -Np1 < $srcdir/gcc-1-mingw-float.patch

  mkdir -p $srcdir/build
  cd $srcdir/build

  unset CFLAGS CXXFLAGS

  chmod ugo+x $srcdir/gcc-$pkgver/configure
  chmod ugo+x $srcdir/gcc-$pkgver/move-if-change

  msg "gcc"
  [ -f Makefile ] || $srcdir/gcc-$pkgver/configure \
     --target=i486-mingw32 \
     --host=$CHOST \
     --build=$CHOST \
     --prefix=/usr \
     --libexecdir=/usr/lib \
     --with-bugurl=https://bugs.archlinux.org/ \
     --enable-languages=c,c++ \
     --enable-shared \
     --enable-sjlj-exceptions \
     --enable-hash-synchronization \
     --disable-nls \
     --disable-libssp \
     --enable-libgomp

  make
}

package() {
  cd $srcdir/build

  make install DESTDIR=$pkgdir

  msg "libgcc"
  make -j1 -C i486-mingw32/libgcc DESTDIR=${pkgdir} libgcc_eh.a install

  msg "libstdc++"
  make -j1 -C i486-mingw32/libstdc++-v3 DESTDIR=${pkgdir} install

  msg "fixes"
  cd $pkgdir/usr/i486-mingw32/lib
  mkdir -p $pkgdir/usr/i486-mingw32/bin/
  for i in `ls -1 *.dll`; do
    ln -s ../lib/$i $pkgdir/usr/i486-mingw32/bin/$i
  done

  cd $pkgdir
  rm -rf usr/bin/i486-mingw32-{gcov,gccbug,gcc-*} \
    usr/{include,lib/libiberty.a} \
    usr/share/{info,man} \
    usr/share/gcc-$pkgver/python

  strip usr/bin/*
  strip usr/lib/gcc/i486-mingw32/$pkgver/{cc1*,collect2}
  i486-mingw32-strip -g usr/lib/gcc/i486-mingw32/$pkgver/*.a
}
