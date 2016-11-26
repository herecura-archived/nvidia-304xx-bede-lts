# vim:set ft=sh et:
# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname="$_pkgname-304xx-bede-lts"
pkgver=304.132
_extramodules=4.4-BEDE-LTS-external
_current_linux_version=4.4.35
_next_linux_version=4.5
pkgrel=10
pkgdesc="NVIDIA 304xx drivers for linux-bede"
arch=('i686' 'x86_64')
url="http://www.nvidia.com/"
makedepends=(
    "linux-bede-lts>=$_current_linux_version"
    "linux-bede-lts-headers>=$_current_linux_version"
    "linux-bede-lts<$_next_linux_version"
    "linux-bede-lts-headers<$_next_linux_version"
    "nvidia-304xx-utils=$pkgver"
    "nvidia-304xx-libgl=$pkgver"
)
conflicts=('nvidia-bede-lts')
license=('custom')
options=('!strip')

source=('disable-mtrr.patch')
source_i686=("http://us.download.nvidia.com/XFree86/Linux-x86/$pkgver/NVIDIA-Linux-x86-$pkgver.run")
source_x86_64=("http://us.download.nvidia.com/XFree86/Linux-x86_64/$pkgver/NVIDIA-Linux-x86_64-$pkgver-no-compat32.run")

sha256sums=('cbaa0c4e4f30d993958c079a22e0346970f99d4fda9d12379777bb16ab3306c9')
sha256sums_i686=('d460f6ab63cc8c1f9fb89a344ad0f3ee1a90e7078b4edba78d86433e66bfd927')
sha256sums_x86_64=('3d7c18eef3044890536b50acab76977112ea5134425b75bba10af37091879ab8')

[[ "$CARCH" = "i686" ]] && _pkg="NVIDIA-Linux-x86-${pkgver}"
[[ "$CARCH" = "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"

prepare() {
    [ -d "$_pkg" ] && rm -rf "$_pkg"
    sh $_pkg.run --extract-only
    cd $_pkg
    # patch if needed

    (
        cd kernel; patch -p1 -i "$srcdir/disable-mtrr.patch"
    )
}

build() {
    _kernver="$(cat /usr/lib/modules/$_extramodules/version)"
    cd $_pkg/kernel
    make SYSSRC=/usr/lib/modules/$_kernver/build module
}

package() {
	depends=(
        "linux-bede-lts>=$_current_linux_version"
        "linux-bede-lts<$_next_linux_version"
        "nvidia-304xx-utils=$pkgver"
        "nvidia-304xx-libgl=$pkgver"
    )
    install=nvidia.install

    install -Dm644 "$srcdir/$_pkg/kernel/nvidia.ko" \
        "$pkgdir/usr/lib/modules/$_extramodules/$_pkgname/nvidia.ko"

    install -dm755 "$pkgdir/usr/lib/modprobe.d"
    echo "blacklist nouveau" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
    echo "blacklist nvidiafb" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"

    # gzip all modules
    find "$pkgdir" -name '*.ko' -exec gzip -9 {} \;

    sed -i -e "s/EXTRAMODULES='.*'/EXTRAMODULES='$_extramodules'/" "$startdir/nvidia.install"
}
