# Maintainer: tas <tasgon_@out/look.com>
# Maintainer: QuantMint <qua/ntmint@/protonm/ail.com>
# Contributor: Cristian Porras <porrascristian@gmail.com>
# Contributor: Matthew Bentley <matthew@mtbentley.us>

_pkgname=godot
pkgname=${_pkgname}-mono-wayland
pkgver=4.1.r1.1765f18
pkgrel=1
pkgdesc="Godot Game Engine: An advanced, feature packed, multi-platform 2D and 3D game engine. (C#/Mono integration)"
url="http://www.godotengine.org"
license=('MIT')
arch=('i686' 'x86_64')
makedepends=('git' 'scons' 'pulseaudio' 'pkgconf' 'xorg-xrandr' 'yasm')                                                                                                          
depends=('glu' 'libxcursor' 'libxinerama' 'alsa-lib' 'freetype2' 'mesa' 'mono' 'dotnet-sdk-6.0' 'msbuild')
optdepends=()
provides=("godot-mono-wayland")
_arch=''
if test "$CARCH" == x86_64; then
  _arch=('x86_64')
else
  _arch=('32')
fi
_bin="godot.linuxbsd.editor.${_arch}.mono"

source=(
	godot-mono.desktop
	icon.png
)
sha256sums=(
	'6edb031a89daf05164277cac706ab797ec3a33fb84e77b8187e40a14dcf7bb02'
	'99f9d17c4355b274ef0c08069cf6e756a98cd5c9d9d22d1b39f79538134277e1'
)

prepare() {
    if [ ! -d "${srcdir}/${_pkgname}" ]
    then
        cd ${srcdir}
        git clone https://github.com/Riteo/godot.git --branch wayland --single-branch --depth 1
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
    sed -n '/\/* Copyright/,/IN THE SOFTWARE./p' main/main.cpp | sed 's/\/\*//' | sed 's/\*\///' > LICENSE
    scons platform=linuxbsd werror=no target=editor module_mono_enabled=yes mono_glue=no $MAKEFLAGS
    ./bin/${_bin} --generate-mono-glue modules/mono/glue
    ./modules/mono/build_scripts/build_assemblies.py --godot-output-dir=./bin --godot-platform=linuxbsd
}

package() {

    cd "${srcdir}"    

    install -Dm644 godot-mono.desktop "${pkgdir}"/usr/share/applications/godot-mono-wayland.desktop
    install -Dm644 icon.png "${pkgdir}"/usr/share/pixmaps/godot-mono-wayland.png
    
    cd "${srcdir}"/${_pkgname}

    install -D -m755 bin/${_bin} "${pkgdir}"/opt/godot-mono-wayland/${_bin}
    cp -R bin/GodotSharp "${pkgdir}"/opt/godot-mono-wayland/GodotSharp
    mkdir -p "${pkgdir}"/usr/bin
    ln -s /opt/godot-mono/${_bin} "${pkgdir}"/usr/bin/godot-mono-wayland

    install -D -m644 LICENSE.txt "${pkgdir}"/usr/share/licenses/godot-mono-wayland/LICENSE
}

