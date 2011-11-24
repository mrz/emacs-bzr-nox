# Maintainer: Mattia Ziulu <mziulu@gmail.com>
# Contributors:
#    Neil Santos <nsantos16+aur@gmail.com> : maintainer of emacs-bzr
#    Jaroslav Lichtblau <dragonlord@aur.archlinux.org> : maintainer of emacs-nox
#

pkgname=emacs-nox-bzr
pkgver=106508
pkgrel=1
pkgdesc='GNU Emacs from official Bzr repository without X11 support'
arch=('i686' 'x86_64')
url='http://www.gnu.org/software/emacs/'
license=('GPL3')
depends=('dbus-core' 'perl' 'gpm')
makedepends=('bzr' 'pkgconfig' 'texinfo')
provides=('emacs')
conflicts=('emacs' 'emacs-nox' 'emacs-otf' 'emacs-cvs' 'emacs-git' 'emacs-bzr')
options=('docs')
install=$pkgname.install

_bzrtrunk='http://bzr.savannah.gnu.org/r/emacs/trunk'
_bzrmod='emacs'

build() {
    cd $srcdir
    msg "Connecting to Savannah..."

    if [[ -d $_bzrmod/.bzr ]]; then
        (cd $_bzrmod && bzr update -v && cd ..)
        msg "Local checkout updated or server timeout"
    else
        bzr co --lightweight -v $_bzrtrunk $_bzrmod
        msg "Checkout done or server timeout"
    fi

    # Copy source to separate build directory and cd into it
    cp -urT $_bzrmod/ ${_bzrmod}-build
    cd ${_bzrmod}-build

    mandir=/usr/share/man
    msg "Starting make..."
    # CFLAGS="${CFLAGS} -fno-optimize-sibling-calls"\
    ./autogen.sh && ./configure --prefix=/usr \
        --libexecdir=/usr/lib \
        --without-x \
        --without-sound || return 1
        #--sysconfdir=/etc \
        #--localstatedir=/var \
        #--mandir=${mandir} \

    # we don't want to use /usr/libexec
    sed -i "s|\"/usr/libexec/emacs.*$|\"/usr/lib/emacs/\"|g" src/epaths.h

    make bootstrap || return 1
    make prefix=${pkgdir}/usr archlibdir=${pkgdir}/usr/lib/emacs/${pkgver}
    make DESTDIR=${pkgdir} install

    msg "Cleaning up..."
    #remove conflict with ctags package
    mv ${pkgdir}/usr/bin/{ctags,ctags.emacs} || return 1
    mv ${pkgdir}/usr/bin/{etags,etags.emacs} || return 1
    mv ${pkgdir}${mandir}/man1/{etags.1,etags.emacs.1}.gz || return 1
    mv ${pkgdir}${mandir}/man1/{ctags.1,ctags.emacs.1}.gz || return 1
    #fix all the 777 perms on directories
    find ${pkgdir}/usr/share/emacs -type d -exec chmod 755 {} \;
    #fix user/root permissions on usr/share files
    find ${pkgdir}/usr/share/emacs -exec chown root.root {} \;

    #chmod 775 ${pkgdir}/var/games
    #chmod 775 ${pkgdir}/var/games/emacs
    chmod 664 ${pkgdir}/usr/var/games/emacs/*
    chown -R root:50 ${pkgdir}/usr/var/games

    #remove empty files
    rm -rf ${pkgdir}/usr/var
    #remove .desktop file and icons
    rm -rf ${pkgdir}/usr/share/{applications,icons}

    #get rid of the package's info directory, install-info adds entries for us at install-time
    rm ${pkgdir}/usr/share/info/dir
    gzip -9nf ${pkgdir}/usr/share/info/*
}