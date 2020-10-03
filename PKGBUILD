# Maintainer: Theophilos Giannakopoulos <theo@abstractnonsense.org>

pkgname=LEd
pkgver="v0.3.2_beta"
pkgrel=1
pkgdesc="2D level editor"
arch=('any')
url="https://deepnight.net/tools/led-2d-level-editor/"
license=('MIT')
depends=('electron')
makedepends=('npm' 'haxe')
source=("$pkgname::git+https://github.com/deepnight/led.git#tag=${pkgver//_/-}"
        "samples-dir.patch"
        "led"
        "LEd.desktop")
sha256sums=('SKIP'
            '9dea55edbc4d5ba1a74a91030ccafc4e417fe97f7ca1cc60b2f0300f51176fdc'
            '8e482648c7f0cafee60c98c0d77d19be1369a273b69b146dc15f91cd989c8716'
            '3eb810890d28424daf2d032646506f4f8c735b2fc034e18ecd7df3a5daaebcbf')

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
  haxelib --always git deepnightLibs https://github.com/deepnight/deepnightLibs.git
  haxelib --always install castle

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
  install -Dm644 -t "${pkgdir}/usr/share/applications/" "LEd.desktop"
  install -Dm755 -t "${pkgdir}/usr/bin/" "led"

  install -Dm644 \
          -t "${pkgdir}/usr/share/LEd" \
          "${pkgname}/app/redist/linux-unpacked/resources/app.asar"

  (
    cd "${pkgname}/app/redist/linux-unpacked/samples"
    for file in $(find . -type f); do
      install -Dm644 \
              "${file}" \
              "${pkgdir}/usr/share/LEd/samples/${file}"
    done
  )
}
