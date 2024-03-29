# Maintainer:  Niko Cantero <Vextium#0001>
# Contributor: Daniel Segesdi <sege/sdi.d/ani@/gma/il.com>
# Contributor: Cristian Porras <porrascristian@gmail.com>
# Contributor: Matthew Bentley <matthew@mtbentley.us>
# Contributor: Peter Jung (ptr1337) <admin at ptr1337 dot dev>

_pkgname="godot"
pkgname="$_pkgname-git"
pkgver=4.2.0.r2733.ga574c0296b
pkgrel=1
pkgdesc="An advanced, feature packed, multi-platform 2D and 3D game engine"
arch=('x86_64')
url="http://www.godotengine.org"
license=('MIT')
depends=(
  #'embree'
  #'mbedtls'

  'alsa-lib'
  'dbus'
  'freetype2'
  'graphite'
  'glu'
  'harfbuzz'
  'harfbuzz-icu'
  'libglvnd'
  'libspeechd'
  'libtheora'
  'libvorbis'
  'libvpx'
  'libwebp'
  'libwslay'
  'libxcursor'
  'libxi'
  'libxinerama'
  'libxrandr'
  'mesa'
  'miniupnpc'
  'opusfile'
  'pcre2'
  'pipewire'
)
makedepends=(
  'gcc'
  'git'
  'mold'
  'pkgconf'
  'scons'
)
optdepends=(
  'godot-export-templates-git: Godot export templates'
)

source=(
  "$_pkgname.desktop"
  "$_pkgname.png"
)
sha256sums=(
  '2ae039a3879b23e157f2125e0b326fa1ef66d56bfd596c790e8943d27652e93a'
  '99f9d17c4355b274ef0c08069cf6e756a98cd5c9d9d22d1b39f79538134277e1'
)

if [ x"$pkgname" != "$_pkgname" ] ; then
  url="https://github.com/godotengine/godot"

  provides=("$_pkgname")
  conflicts=("$_pkgname")

  _pkgsrc="$_pkgname"

  source+=(
    "$_pkgsrc"::"git+$url.git"
  )
  sha256sums+=(
    'SKIP'
  )

  pkgver() {
    cd "$srcdir/$_pkgsrc"

    local _file="version.py"

    local _major=$(grep 'major' $_file | sed 's/major = //')
    local _minor=$(grep 'minor' $_file | sed 's/minor = //')
    local _patch=$(grep 'patch' $_file | sed 's/patch = //')
    local _version="$_major.$_minor.$_patch"

    local _line=$(grep -n 'major' $_file | cut -d':' -f1)
    local _commit=$(
      git blame -L $_line,$((_line+2)) -- version.py \
        | sed -E 's@([a-f0-9]+)\s.*\s([0-9]{4}-[0-9]{2}-[0-9]{2})\s(.*)$@\1 \2 \3@' \
        | sort -r -k 2 | head -1 |  awk '{print $1;}'
    )
    local _revision=$(
      git rev-list --count $_commit..HEAD
    )
    local _hash=$(
      git rev-parse --short HEAD
    )

    printf "%s.r%s.g%s" \
      "$_version" \
      "$_revision" \
      "$_hash"
  }
fi

prepare() {
  # Disable the check that adds -no-pie to LINKFLAGS, for gcc != 6
  sed -i 's,0] >,0] =,g' "$srcdir/$_pkgsrc/platform/linuxbsd/detect.py"
}

build() {
  cd "$srcdir/$_pkgsrc"

  local _to_unbundle=(
    ## Not unbundled (yet):
    #enet # no upstreamed IPv6 support
    #libsquish
    #recast
    #xatlas

    ## Not working?
    #certs
    #embree
    #mbedtls

    ## AUR
    #libwebm
    #squish

    ## All or None
    freetype
    graphite
    harfbuzz
    libpng
    zlib

    ## Others
    libogg
    libtheora
    libvorbis
    libvpx
    libwebp
    miniupnpc
    opus
    pcre2
    wslay
    zstd
  )
  local _system_libs=()
  for _lib in "${_to_unbundle[@]}" ; do
    _system_libs+="builtin_${_lib}=no "
    rm -rf "$srcdir/$_pkgsrc/thirdparty/$_lib"
  done

  local _scons_options=(
    platform=linuxbsd
    target=editor
    production=yes
    werror=no

    bits=64
    linker=mold
    use_lto=yes
    deprecated=no
    compiledb=yes
    system_certs_path=/etc/ssl/certs/ca-certificates.crt
    CFLAGS="$CFLAGS -fPIC -Wl,-z,relro,-z,now -w"
    CXXFLAGS="$CXXFLAGS -fPIC -Wl,-z,relro,-z,now -w"
    LINKFLAGS="$LDFLAGS"
    ${_system_libs[@]}
  )

  export BUILD_NAME=arch_linux
  scons "${_scons_options[@]}"
}

package() {
    install -Dm644 "$srcdir/godot.desktop" \
      -t "$pkgdir/usr/share/applications/"

    install -Dm644 "$srcdir/$_pkgname.png" \
      -t "$pkgdir/usr/share/pixmaps/"

    install -Dm755 "$srcdir/$_pkgsrc/bin/godot.linuxbsd.editor.${arch}" \
      "$pkgdir/usr/bin/$_pkgname"

    install -Dm644 "$srcdir/$_pkgsrc/LICENSE.txt" \
      "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}

