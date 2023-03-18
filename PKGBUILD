_gitname=vboot_reference
_gitver=release-R112-15359.B
_gitroot="https://chromium.googlesource.com/chromiumos/platform/${_gitname}"
_gitbranch="${_gitver}"
pkgname=("vboot-morphius") 
pkgdesc="ChromeOS vbutil tools: futility (vbutil_kernel), cgpt, crossystem ..."
url="https://chromium.googlesource.com/chromiumos/platform/${_gitname}"
license=('GPL')
makedepends=('git' 'libyaml')
depends=('libutil-linux' 'openssl' 'linux-morphius')
conflicts=("vboot-utils" "chromeos-vboot-reference-crossystem-git" "chromeos-vboot-reference-git")
provides=("vboot-utils" "chromeos-vboot-reference-crossystem-git" "chromeos-vboot-reference-git")
arch=('x86_64')
pkgver=112
pkgrel=1

source=(change-acpi-path.patch)

sha256sums=('29a84b6370289ed3f51a531ca5f894565a515541650ad0acd49a1ddf3bfdf873')

prepare() {
  if [[ -d "${srcdir}/${_gitname}/" ]]; then
    cd "${srcdir}/${_gitname}/"
    git reset --hard
    git pull --depth=1 origin "${_gitbranch}:${_gitbranch}"
    git checkout "${_gitbranch}"
    echo
  else
    cd "${srcdir}/"
    git clone --branch "${_gitbranch}" --depth=1 "${_gitroot}" "${srcdir}/${_gitname}/"
    echo
  fi

  # Apply patch
  cd "${srcdir}/${_gitname}/"
  git apply "${srcdir}/change-acpi-path.patch"
}

build() {
  cd "${srcdir}/${_gitname}"
  sed -i 's/TEST_NAMES += ${TLCL_TEST_NAMES}/TEST_NAMES =/' ./Makefile
  export CFLAGS+=" -Wno-error=deprecated-declarations"  # Skip deprecated: Since OpenSSL 3.0
  # install_for_test also builds crossystem
  make TEST_INSTALL_DIR=../build install_for_test
}

package() {
  #For some reason the ../build dir is necessary because ${pkgdir} is created with insufficient permissions when make DESTDIR=${pkgdir} install => copy contents of ../build
  cd "${srcdir}/build"
  find usr/* -type f -exec install -vDm 755 "{}" "${pkgdir}/{}" \;
  
  cd "${srcdir}/${_gitname}/tests"
  find devkeys -maxdepth 1 -type f -exec install -vDm 755 "{}" "${pkgdir}/usr/share/vboot-{}" \;
}
