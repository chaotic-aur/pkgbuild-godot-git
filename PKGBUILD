# Maintainer:  Niko Cantero <Vextium#0001>
# Contributor: Daniel Segesdi <sege/sdi.d/ani@/gma/il.com>
# Contributor: Cristian Porras <porrascristian@gmail.com>
# Contributor: Matthew Bentley <matthew@mtbentley.us>
# Contributor: tas <tasgon_@out/look.com>
# Contributor: QuantMint <qua/ntmint@/protonm/ail.com>

_pkgname=godot
pkgname=${_pkgname}-git
pkgver=4.0.r0.g
pkgrel=1
pkgdesc="Godot Game Engine: An advanced, feature packed, multi-platform 2D and 3D game engine. Git version."
url="http://www.godotengine.org"
license=('MIT')
arch=('i686' 'x86_64')
makedepends=('gcc' 'git' 'scons' 'pkgconf' 'yasm' 'mold')
depends=('alsa-lib' 'glu' 'libglvnd' 'libxcursor' 'libxinerama' 'libxi' 'libxrandr' 'mesa' 'pipewire-pulse' 'systemd-libs')
optdepends=()
conflicts=("${_pkgname}")
provides=("${_pkgname}")
_arch=''
if test "$CARCH" == x86_64; then
  _arch=('x86_64')
else
  _arch=('32')
fi

source=(
	godot.desktop
	icon.png
)
sha256sums=(
	'2ae039a3879b23e157f2125e0b326fa1ef66d56bfd596c790e8943d27652e93a'
	'99f9d17c4355b274ef0c08069cf6e756a98cd5c9d9d22d1b39f79538134277e1'
)

prepare() {
    if [ ! -d "${srcdir}/${_pkgname}" ]
    then
        cd ${srcdir}
        git clone https://github.com/godotengine/godot.git --branch master --single-branch --depth 1
    else
        cd "${srcdir}/${_pkgname}"
        git fetch --depth 1 origin master
        git reset --hard origin/master
    fi
}

pkgver() {
    cd "${srcdir}/${_pkgname}"
    _major=$(cat version.py|grep "major" | sed 's/major = //')
    _minor=$(cat version.py|grep "minor" | sed 's/minor = //')
    _revision=$(printf "r%s.%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)")
    echo "${_major}.${_minor}.${_revision}"
}

build() {
    cd "${srcdir}"/${_pkgname}
    scons p=linuxbsd target=editor deprecated=no bits=64 use_lto=yes udev=yes compiledb=yes linker=mold -j$((`nproc`+1))
}

package() {

    cd "${srcdir}"

    install -Dm644 godot.desktop "${pkgdir}"/usr/share/applications/godot.desktop
    install -Dm644 icon.png "${pkgdir}"/usr/share/pixmaps/godot.png

    cd "${srcdir}"/${_pkgname}

    install -D -m755 bin/godot.linuxbsd.editor.${_arch} "${pkgdir}"/usr/bin/godot
    install -D -m644 LICENSE.txt "${pkgdir}"/usr/share/licenses/godot-git/LICENSE
}

