# Maintainer: Tony Lambiris <tony@criticalstack.com>

pkgname=cri-o
pkgver=1.12.1
pkgrel=1
pkgdesc='Open Container Initiative-based implementation of Kubernetes Container Runtime Interface'
arch=('x86_64' 'aarch64')
url='https://github.com/kubernetes-incubator/cri-o'
license=(Apache)
makedepends=(go go-md2man ostree)
backup=('etc/crio/crio.conf')
source=("git+https://github.com/kubernetes-incubator/cri-o#tag=v$pkgver")
sha256sums=('SKIP')

build() {
	cd "${pkgname}"

	make -j1 binaries docs

	./bin/crio --selinux=true \
		--storage-driver=overlay \
		--conmon /usr/libexec/crio/conmon \
		--cni-plugin-dir /usr/libexec/cni \
		--default-mounts /run/secrets \
		--cgroup-manager=systemd config > crio.conf
}

package() {
	cd "${pkgname}"
	make install install.systemd PREFIX="$pkgdir/usr"

	# fix-up paths pointing to /usr/local to /usr
	sed -i --follow-symlinks -re 's|/usr/local|/usr|g' $pkgdir/usr/lib/systemd/system/*.service

	# install configs
	install -dm755 $pkgdir/etc/crio/
	install -Dm644 crio.conf $pkgdir/etc/crio/crio.conf
	install -Dm644 seccomp.json $pkgdir/etc/crio/seccomp.json
}
