# Maintainer: Mattia Ziulu <mziulu@gmail.com>
#
# Contributors:
#    Neil Santos <nsantos16+aur@gmail.com> : maintainer of emacs-bzr
#    Jaroslav Lichtblau <dragonlord@aur.archlinux.org> : maintainer of emacs-nox
#

_opt_puresize="1820000"

pkgname=emacs-nox-bzr
_pkgname=emacs
pkgver=108437
pkgrel=1
pkgdesc='GNU Emacs from official Bzr repository without X11 support'
arch=('arm' 'i686' 'x86_64')
url='http://www.gnu.org/software/emacs/'
license=('GPL3')
depends=('dbus-core' 'perl' 'ncurses')
makedepends=('bzr' 'pkgconfig' 'texinfo')
provides=("${_pkgname}=${pkgver}")
conflicts=('emacs' 'emacs-nox' 'emacs-otf' 'emacs-cvs' 'emacs-git' 'emacs-bzr')
options=('docs')
source=("${_pkgname}::bzr+http://bzr.savannah.gnu.org/r/$_pkgname/trunk/")
md5sums=('SKIP')
install=$pkgname.install

_mandir=/usr/share/man

pkgver() {
    bzr revno "${srcdir}/${_pkgname}"
}

prepare() {
    cd "${srcdir}/${_pkgname}"

    msg "Adjusting BASE_PURESIZE to avoid possible leaks"
    sed -i -e "s/\(define BASE_PURESIZE\s*(*\)[0-9]*/\1${_opt_puresize}/" src/puresize.h
}

build() {
    cd "${srcdir}/${_pkgname}"

    msg "Starting make..."
    ./autogen.sh && ./configure --prefix=/usr \
        --libexecdir=/usr/lib \
        --sysconfdir=/etc \
        --localstatedir=/var \
        --mandir=${_mandir} \
        --without-x \
        --without-sound

    make
}
package() {
    cd "${srcdir}/${_pkgname}"
    make DESTDIR="${pkgdir}" install

    msg "Cleaning up..."
    # Remove conflict with ctags package
    mv "${pkgdir}"/usr/bin/{ctags,ctags.emacs}
    mv "${pkgdir}"/usr/bin/{etags,etags.emacs}
    mv "${pkgdir}"${_mandir}/man1/{etags.1,etags.emacs.1}.gz
    mv "${pkgdir}"${_mandir}/man1/{ctags.1,ctags.emacs.1}.gz

    # This is mostly superfluous, and conflicts with texinfo
    rm "${pkgdir}"/usr/share/info/info.info.gz
    rm "${pkgdir}"/usr/share/info/dir

    # Fix all the 777 perms on directories
    find "${pkgdir}"/usr/share/emacs -type d -exec chmod 755 {} \;
    #fix user/root permissions on usr/share files
    find "${pkgdir}"/usr/share/emacs -exec chown root.root {} \;
    # Fix perms on /var/games
    chmod 775 "${pkgdir}"/var/games
    chmod 775 "${pkgdir}"/var/games/emacs
    chmod 664 "${pkgdir}"/var/games/emacs/*
    chown -R root:games "${pkgdir}"/var/games

    # Remove .desktop file and icons
    rm -rf "${pkgdir}"/usr/share/{applications,icons}
}
