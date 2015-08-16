pkgname=lib32-mingw-w64-allegro4-static
pkgver=4.4.2
pkgrel=1
pkgdesc="Portable library mainly aimed at video game and multimedia programming (legacy version) (mingw-w64, 32-bit, static)"
arch=(any)
url="http://alleg.sourceforge.net"
license=("custom")
makedepends=(mingw-w64-gcc cmake)
depends=(mingw-w64-crt)
conflicts=(mingw-w64-allegro4)
provides=(mingw-w64-allegro4)
options=(!libtool !strip !buildflags)
source=("http://downloads.sourceforge.net/project/alleg/allegro/$pkgver/allegro-$pkgver.tar.gz"
"Toolchain-i686-w64-mingw32.cmake")
md5sums=('4db71b0460fc99926ae91d223199c2e6'
         'da10ad00a161800e0b011a77c3efbf76')

_architectures="i686-w64-mingw32"

_optimal_make_jobs() {
  if [ -r /proc/cpuinfo ]; then
    local core_count=$(grep -Fc processor /proc/cpuinfo)
  else
    local core_count=0
  fi
  if [ $core_count -gt 1 ]; then
    echo -n $[$core_count-1]
  else
    echo -n 1
  fi
}

build() {
  cd "$srcdir/allegro-$pkgver"
  unset LDFLAGS
  for _arch in ${_architectures}; do
    mkdir "build-${_arch}" && pushd "build-${_arch}"
    PATH="/usr/${_arch}/bin:$PATH" cmake \
      -DCMAKE_INSTALL_PREFIX:PATH=/usr/${_arch} \
      -DCMAKE_TOOLCHAIN_FILE=${srcdir}/Toolchain-${_arch}.cmake \
      -DWANT_EXAMPLES:BOOL=OFF \
      -DWANT_TESTS:BOOL=OFF \
      -DWANT_TOOLS:BOOL=OFF \
      -DSHARED:BOOL=OFF \
      ..
    make -j$(_optimal_make_jobs)
    popd
  done
}

package() {

  for _arch in ${_architectures}; do
    cd "$srcdir/allegro-$pkgver/build-$_arch"
    make DESTDIR="$pkgdir" install
    mkdir -p "$pkgdir/usr/$_arch"
    mv "$pkgdir/usr/"{include,lib} "$pkgdir/usr/$_arch"
    find "$pkgdir/usr/${_arch}" -name '*.dll' | xargs -rtl1 ${_arch}-strip -x
    find "$pkgdir/usr/${_arch}" -name '*.a' -o -name '*.dll' | xargs -rtl1 ${_arch}-strip -g
  done
}
