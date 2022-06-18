# Maintainer: Eli Schwartz <eschwartz@archlinux.org>
# Contributor: Iacopo Isimbaldi <isiachi@rhye.it>

# All my PKGBUILDs are managed at https://github.com/eli-schwartz/pkgbuilds

pkgname=zfs-dkms-git
pkgver=2.1.99.r1266.gd51f4ea5f9
pkgrel=1
epoch=2
pkgdesc="Kernel modules for the Zettabyte File System."
arch=('any')
url="https://zfsonlinux.org/"
license=('CDDL')
makedepends=('git')
conflicts=("${pkgname%-git}" 'spl-dkms')
provides=("ZFS-MODULE=${pkgver}" "SPL-MODULE=${pkgver}" "${pkgname%-git}=${pkgver}" 'spl-dkms')
# ambiguous, provided for backwards compat, pls don't use
provides+=('zfs')
replaces=('spl-dkms-git')
source=("git+https://github.com/zfsonlinux/zfs.git")
sha256sums=('SKIP')
b2sums=('SKIP')

pkgver() {
    cd "${srcdir}"/zfs

    git describe --long | sed 's/^zfs-//;s/-rc/rc/;s/\([^-]*-g\)/r\1/;s/-/./g'
}

prepare() {
    cd "${srcdir}"/zfs

    #patch -p1 -i ../0001-only-build-the-module-in-dkms.conf.patch

    # remove unneeded sections from module build
    sed -ri "/AC_CONFIG_FILES/,/]\)/{
/AC_CONFIG_FILES/n
/]\)/n
/^\s*(module\/.*|zfs.release|Makefile)/!d
}" configure.ac

    autoreconf -fi
}

build() {
    cd "${srcdir}"/zfs

    ./scripts/dkms.mkconf -n zfs -v ${pkgver} -f dkms.conf
    # update metadata
    ./scripts/make_gitrev.sh
    _meta_release=${pkgver#*.r}
    sed -i -e "s/Release:[[:print:]]*/Release:      ${_meta_release/./_}/" META
}

package() {
    depends=("zfs-utils-git=${epoch}:${pkgver}" 'dkms')

    cd "${srcdir}"/zfs

    dkmsdir="${pkgdir}/usr/src/zfs-${pkgver}"
    install -d "${dkmsdir}"/{config,scripts}
    cp -a configure dkms.conf Makefile.in META zfs_config.h.in zfs.release.in include/ module/ "${dkmsdir}"/
    cp config/compile config/config.* config/missing config/*sh "${dkmsdir}"/config/
    cp scripts/enum-extract.pl scripts/dkms.postbuild "${dkmsdir}"/scripts/
}
