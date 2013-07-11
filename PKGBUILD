# Maintainer: Mattia Ziulu <mziulu@gmail.com>
#
# Contributors:
#    Neil Santos <nsantos16+aur@gmail.com> : maintainer of emacs-bzr
#    Jaroslav Lichtblau <dragonlord@aur.archlinux.org> : maintainer of emacs-nox
#

pkgname=emacs-nox-bzr
pkgver=108437
pkgrel=1
pkgdesc='GNU Emacs from official Bzr repository without X11 support'
arch=('arm' 'i686' 'x86_64')
url='http://www.gnu.org/software/emacs/'
license=('GPL3')
depends=('dbus-core' 'perl' 'ncurses')
makedepends=('bzr' 'pkgconfig' 'texinfo')
provides=('emacs')
conflicts=('emacs' 'emacs-nox' 'emacs-otf' 'emacs-cvs' 'emacs-git' 'emacs-bzr')
options=('docs')
install=$pkgname.install

_bzrtrunk='http://bzr.savannah.gnu.org/r/emacs/trunk'
_bzrmod='emacs'

pkgver() {
  bzr revno "$_bzrtrunk"
}

prepare() {
    cd $srcdir
    msg "Connecting to Savannah..."

    if [[ -d $_bzrmod/.bzr ]]; then
        (cd $_bzrmod && bzr update -v && cd ..)
        msg "Local checkout updated or server timeout"
    else
        bzr co --lightweight -v $_bzrtrunk $_bzrmod
        msg "Checkout done or server timeout"
    fi
}

build() {
    # Copy source to separate build directory and cd into it
    cp -urT $_bzrmod/ ${_bzrmod}-build
    cd ${_bzrmod}-build

    msg "Starting make..."
    ./autogen.sh && ./configure --prefix=/usr \
        --libexecdir=/usr/lib \
        --sysconfdir=/etc \
        --localstatedir=/var \
        --mandir=/usr/share/man \
        --without-x \
        --without-sound

    make bootstrap
    make
}
package() {
    cd ${_bzrmod}-build

    make DESTDIR=${pkgdir} install

    msg "Cleaning up..."
    # Remove conflict with ctags package
    mv ${pkgdir}/usr/bin/{ctags,ctags.emacs}
    mv ${pkgdir}/usr/bin/{etags,etags.emacs}
    mv ${pkgdir}/usr/share/man/man1/{etags.1,etags.emacs.1}.gz
    mv ${pkgdir}/usr/share/man/man1/{ctags.1,ctags.emacs.1}.gz

    # This is mostly superfluous, and conflicts with texinfo
    rm $pkgdir/usr/share/info/info.info.gz
    rm $pkgdir/usr/share/info/dir

    # Fix all the 777 perms on directories
    find ${pkgdir}/usr/share/emacs -type d -exec chmod 755 {} \;
    #fix user/root permissions on usr/share files
    find ${pkgdir}/usr/share/emacs -exec chown root.root {} \;
    # Fix perms on /var/games
    chmod 775 ${pkgdir}/var/games
    chmod 775 ${pkgdir}/var/games/emacs
    chmod 664 ${pkgdir}/var/games/emacs/*
    chown -R root:games ${pkgdir}/var/games

    # Remove .desktop file and icons
    rm -rf ${pkgdir}/usr/share/{applications,icons}
}
