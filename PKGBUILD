# vim:set ft=sh et:
# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname="$_pkgname-304xx-bede"
pkgver=304.131
_extramodules=4.4-BEDE-LTS-external
_current_linux_version=4.4.24
_next_linux_version=4.5
pkgrel=2
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

source=('disable-mtrr.patch' 'linux-4.6.patch')
source_i686=("http://us.download.nvidia.com/XFree86/Linux-x86/$pkgver/NVIDIA-Linux-x86-$pkgver.run")
source_x86_64=("http://us.download.nvidia.com/XFree86/Linux-x86_64/$pkgver/NVIDIA-Linux-x86_64-$pkgver-no-compat32.run")

sha256sums=('cbaa0c4e4f30d993958c079a22e0346970f99d4fda9d12379777bb16ab3306c9'
            '7133fb270c138ab697271245f3c05654f3f409e98d9b67b01d513403f7fcc5db')
sha256sums_i686=('d2554bb6f7867e7762d0ecedcac5bde7de0634e43b952bf466323ea8b4032da8')
sha256sums_x86_64=('2f6e82c79ed4d1ac3d42b2c0f71d4fbdd9293db801de396e7e2cc3fdcafaf83e')

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

    patch -p1 -i "$srcdir/linux-4.6.patch"
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
