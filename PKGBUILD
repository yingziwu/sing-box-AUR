# Maintainer: everyx <lunt.luo#gmail.com>

pkgname=sing-box
pkgver=1.2.6
pkgrel=2

pkgdesc='The universal proxy platform.'
arch=('x86_64' 'i686')
url='https://sing-box.sagernet.org/'
license=('GPL3 with name use or association addition')

makedepends=('go' 'patch')

source=(
    "$pkgname-$pkgver.tar.gz::https://github.com/SagerNet/sing-box/archive/v$pkgver.tar.gz"
    "sing-box.sysusers"
    "sing-box.tmpfiles"
    "0001-sing-box-service.patch"
)
sha256sums=(
    '8f7adf55ed9afe6ec0dd8b04ed64dd3a6243578ee779f909dfb3778fa2dbda10'
    '35b9966604f3e5cf2acf9b86a7a1826b73829d59e04f283acaf89e84d9f4f4f7'
    '83f27690c2b37823b4de56d890462ac32118c6984554e32b4f6d25b93426a94f'
    'b7a21a59ce2ec82fdded6b8a9e337c4f43e85a0688ad4a26f0f601aefa44dc54'
)

conflicts=("$pkgname-git" "$pkgname-beta")
optdepends=(
    'sing-geosite: sing-geosite database'
    'sing-geoip: sing-geoip database'
)

backup=("etc/$pkgname/config.json")

_tags=with_gvisor,with_quic,with_wireguard,with_utls,with_reality_server,with_clash_api
build() {
    cd "$pkgname-$pkgver"

    export CGO_CPPFLAGS="$CPPFLAGS"
    export CGO_CFLAGS="$CFLAGS"
    export CGO_CXXFLAGS="$CXXFLAGS"
    export CGO_LDFLAGS="$LDFLAGS"

    go build \
        -v \
        -trimpath \
        -buildmode=pie \
        -mod=readonly \
        -modcacherw \
        -tags "$_tags" \
        -ldflags "
            -X \"github.com/sagernet/sing-box/constant.Version=$pkgver\"
            -s -w -buildid= -linkmode=external" \
        ./cmd/sing-box

    cd release/config/
    patch -p1 <"$srcdir/0001-sing-box-service.patch"
}

package() {
    cd "$pkgname-$pkgver"

    install -Dm644 LICENSE -t "$pkgdir/usr/share/licenses/$pkgname"
    install -Dm755 "$pkgname" -t "$pkgdir/usr/bin"
    install -Dm644 "release/config/config.json" -t "$pkgdir/etc/$pkgname"
    install -Dm644 "release/config/$pkgname.service" -t "$pkgdir/usr/lib/systemd/system"
    install -Dm644 "release/config/$pkgname@.service" -t "$pkgdir/usr/lib/systemd/system"

    install -Dm644 "$srcdir/sing-box.sysusers" "$pkgdir/usr/lib/sysusers.d/$pkgname.conf"
    install -Dm644 "$srcdir/sing-box.tmpfiles" "$pkgdir/usr/lib/tmpfiles.d/$pkgname.conf"
}
