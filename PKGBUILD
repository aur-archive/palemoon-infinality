# Maintainer:  WorMzy Tykashi <wormzy.tykashi@gmail.com>
# Contributor: jaro3@seznam.cz
pkgname=palemoon-infinality
pkgver=25.4.1
pkgrel=1
pkgdesc="Open source web browser based on Firefox focusing on efficiency."
arch=('i686' 'x86_64')
url="http://www.palemoon.org/"
license=('MPL' 'GPL' 'LGPL')
depends=('gtk2' 'dbus-glib' 'desktop-file-utils' 'libxt' 'mime-types' 'nss' 'alsa-lib')
makedepends=('zip' 'unzip' 'freetype2' 'fontconfig' 'pkg-config' 'iw' 'libidl2' 'python2' 
             'curl' 'libnotify' 'mesa' 'autoconf2.13' 'yasm' 'gstreamer0.10' 'git'
             'gstreamer0.10-base-plugins'
             'gstreamer0.10-good-plugins'
             'gstreamer0.10-bad-plugins'
             'gstreamer0.10-ugly-plugins'
             'gstreamer0.10-ffmpeg'
)
optdepends=('networkmanager: Location detection via available WiFi networks'
            'libpulse: PulseAudio audio driver'
            'hunspell: spell checker and morphological analyzer'
            'hyphen: library for hyphenation and justification')
install=palemoon.install
source=(git+"https://github.com/MoonchildProductions/Pale-Moon#tag=${pkgver}_Release"
        palemoon.desktop
        mozconfig.in
	infinality.patch)
md5sums=('SKIP'
         '32231f6e6a532021fd04c6d7b32f4270'
         '9ff0a5a4cfa930148cb96612bed4fe09'
	 'SKIP')

prepare() {
	cd "$srcdir"
	patch -p1 -i $srcdir/infinality.patch
}
build() {
	export MOZBUILD_STATE_PATH="$srcdir/mozbuild"
	export MOZCONFIG="$srcdir/mozconfig"
	echo  MOZBUILD_STATE_PATH=$MOZBUILD_STATE_PATH
	echo  MOZCONFIG=$MOZCONFIG
	sed 's#%SRCDIR%#'"$srcdir"'#g' mozconfig.in > mozconfig
	
	cd "$srcdir/Pale-Moon"
	chmod -R +x build/autoconf/* python/*
	find . -name '*.sh' -exec chmod +x {} \;

	CPPFLAGS="$CPPFLAGS -O2"
	python2 mach build || echo "Next =>"
	python2 mach build
}

package() {
  cd "$srcdir/pmbuild"
  make package
  cd dist
  install -d "$pkgdir"/usr/{bin,lib}
  cp -r palemoon/ "$pkgdir/usr/lib/$pkgname"
  ln -s "../lib/$pkgname/palemoon" "$pkgdir/usr/bin/palemoon"
  install -Dm644 "$srcdir/palemoon.desktop" "$pkgdir/usr/share/applications/$pkgname.desktop"

  # icons
  install -Dm644 palemoon/browser/chrome/icons/default/default16.png \
    "$pkgdir/usr/share/icons/hicolor/16x16/apps/$pkgname.png"
  install -Dm644 palemoon/browser/chrome/icons/default/default32.png \
    "$pkgdir/usr/share/icons/hicolor/32x32/apps/$pkgname.png"
  install -Dm644 palemoon/browser/chrome/icons/default/default48.png \
    "$pkgdir/usr/share/icons/hicolor/48x48/apps/$pkgname.png"
  install -Dm644 palemoon/browser/icons/mozicon128.png \
    "$pkgdir/usr/share/icons/hicolor/128x128/apps/$pkgname.png"

  # use system-provided dictionaries
  rm -rf "$pkgdir"/usr/lib/$pkgname/{dictionaries,hyphenation}
  ln -s /usr/share/hunspell "$pkgdir/usr/lib/$pkgname/dictionaries"
  ln -s /usr/share/hyphen "$pkgdir/usr/lib/$pkgname/hyphenation"

  # avoid duplicate binaries
  # https://bugzilla.mozilla.org/show_bug.cgi?id=658850
  #ln -sf palemoon "$pkgdir/usr/lib/$pkgname/palemoon-bin"
  rm -f "$pkgdir/usr/lib/$pkgname/palemoon-bin"
}
