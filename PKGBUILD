# Maintainer: Theophilos Giannakopoulos <theo@abstractnonsense.org>

pkgname=LDtk
pkgver="v0.5.0_beta"
pkgrel=1
pkgdesc="Level Designer Toolkit: a 2D level editor"
arch=('any')
url="https://deepnight.net/tools/ldtk-2d-level-editor/"
license=('MIT')
depends=('electron')
makedepends=('npm' 'haxe')
replaces=('LEd')
source=("$pkgname::git+https://github.com/deepnight/ldtk.git#tag=${pkgver//_/-}"
        "samples-dir.patch"
        "ldtk"
        "LDtk.desktop")
sha256sums=('SKIP'
            '0c4ff249d3ca26738ece8b9417ecffbfe318e7a5111078669d797e846e86a10c'
            'da01d21b815c928cf0451cc9e7dce5af6fae2f6b9d4be0975d44229b9d4fc29b'
            'a846ddb7ee95f65c4c51ba5206753baeac535961949253f9343ca188595eaf9f')

prepare() {
  git -C "$srcdir/$pkgname" apply "$srcdir/samples-dir.patch"
}

build() {
  cd "$srcdir/$pkgname"

  haxelib newrepo
  haxelib --always git heaps https://github.com/HeapsIO/heaps.git
  haxelib --always git hxnodejs https://github.com/HaxeFoundation/hxnodejs.git
  haxelib --always git electron https://github.com/tong/hxelectron.git
  haxelib --always git deepnightLibs https://github.com/deepnight/deepnightLibs.git
  haxelib --always git ldtk-haxe-api https://github.com/deepnight/ldtk-haxe-api.git
  haxelib --always git castle https://github.com/ncannasse/castle.git

  (
    cd app
    npm install
  )

  haxe main.debug.hxml
  haxe renderer.debug.hxml

  (
    cd app
    npm run compile
    npx electron-builder build --linux \
        --publish never \
        --dir \
        -c.electronDist=/usr/lib/electron \
        -c.electronVersion="$(</usr/lib/electron/version)"
  )
}

package() {
  install -Dm644 -t "${pkgdir}/usr/share/applications/" "LDtk.desktop"
  install -Dm755 -t "${pkgdir}/usr/bin/" "ldtk"

  install -Dm644 \
          -t "${pkgdir}/usr/share/ldtk/" \
          "${pkgname}/app/redist/linux-unpacked/resources/app.asar"

  install -Dm644 \
          -t "${pkgdir}/usr/share/licenses/ldtk/" \
          "${pkgname}/LICENSE"
  # The READMEs include required attributions for included components.
  install -Dm644 \
          -t "${pkgdir}/usr/share/licenses/ldtk/" \
          "${pkgname}/README.md"
  install -Dm644 \
          -t "${pkgdir}/usr/share/licenses/ldtk/samples/" \
          "${pkgname}/app/samples/README.md"

  (
    cd "${pkgname}/app/redist/linux-unpacked/samples"
    for file in $(find . -type f); do
      install -Dm644 \
              "${file}" \
              "${pkgdir}/usr/share/LDtk/samples/${file}"
    done
  )
}
