# Maintainer:  Niko Cantero <Vextium#0001>
# Contributor: Daniel Segesdi <sege/sdi.d/ani@/gma/il.com>
# Contributor: Cristian Porras <porrascristian@gmail.com>
# Contributor: Matthew Bentley <matthew@mtbentley.us>
# Contributor: Peter Jung (ptr1337) <admin at ptr1337 dot dev>

_arch='x86_64'
_pkgname=godot
pkgname=${_pkgname}-git
pkgver=4.0.r47964.g3a59c833f1
pkgrel=1
pkgdesc="Godot Game Engine: An advanced, feature packed, multi-platform 2D and 3D game engine. Git version."
arch=("${_arch}")
url="http://www.godotengine.org"
license=('MIT')
depends=('embree' 'glu' 'freetype2' 'libglvnd' 'libtheora' 'libvorbis' 'libvpx' 'libwebp' 'alsa-lib' 'pipewire'
         'libwslay' 'libxcursor' 'libxi' 'libxinerama' 'libxrandr' 'mbedtls' 'miniupnpc' 'mesa' 'opusfile')
makedepends=('gcc' 'git' 'scons' 'pkgconf' 'yasm' 'mold')
optdepends=('godot-export-templates-git: Godot export templates')
provides=("${_pkgname}")
conflicts=("${_pkgname}")
source=(
	"godot::git+https://github.com/godotengine/${_pkgname}.git#branch=master"
	'godot.desktop'
	'icon.png')
sha256sums=(
	'SKIP'
	'2ae039a3879b23e157f2125e0b326fa1ef66d56bfd596c790e8943d27652e93a'
	'99f9d17c4355b274ef0c08069cf6e756a98cd5c9d9d22d1b39f79538134277e1')

prepare() {
    if [ ! -d "${srcdir}/${_pkgname}" ]
    then
        cd ${srcdir}
        git clone https://github.com/godotengine/godot.git --branch master --single-branch
    else
        cd "${srcdir}/${_pkgname}"
        git fetch origin master
        git reset --hard origin/master
    fi
}

pkgver() {
    cd "${srcdir}/${_pkgname}"
    _major=$(cat version.py|grep "major" | sed 's/major = //')
    _minor=$(cat version.py|grep "minor" | sed 's/minor = //')
    _revision=$(printf "r%s.g%s" "$(git rev-list --count HEAD)" "$(git rev-parse --short HEAD)")
    echo "${_major}.${_minor}.${_revision}"
}

build() {
  # Not unbundled (yet):
  #  enet (contains no upstreamed IPv6 support)
  #  libsquish, recast, xatlas
  #  AUR: libwebm, squish
  local to_unbundle="certs embree freetype libogg libpng libtheora libvorbis libvpx libwebp mbedtls miniupnpc opus pcre2 wslay zlib zstd"
  local system_libs=""
  for _lib in $to_unbundle; do
    system_libs+="builtin_"$_lib"=no "
    rm -rf thirdparty/$_lib
  done
     
  cd "${srcdir}"/${_pkgname}
  export BUILD_NAME=arch_linux
  scons -j$((`nproc`+1))
    platform=linuxbsd \
    target=editor \
    bits=64 \
    linker=mold \
    use_lto=yes \
    deprecated=no \
    compiledb=yes \
    system_certs_path=/etc/ssl/certs/ca-certificates.crt \
    CFLAGS="$CFLAGS -fPIC -Wl,-z,relro,-z,now -w" \
    CXXFLAGS="$CXXFLAGS -fPIC -Wl,-z,relro,-z,now -w" \
    LINKFLAGS="$LDFLAGS" \
    $system_libs
}

package() {

    cd "${srcdir}"

    install -Dm644 godot.desktop "${pkgdir}"/usr/share/applications/godot.desktop
    install -Dm644 icon.png "${pkgdir}"/usr/share/pixmaps/godot.png

    cd "${srcdir}"/${_pkgname}

    install -D -m755 bin/godot.linuxbsd.editor.${_arch} "${pkgdir}"/usr/bin/godot
    install -D -m644 LICENSE.txt "${pkgdir}"/usr/share/licenses/godot-git/LICENSE
}

