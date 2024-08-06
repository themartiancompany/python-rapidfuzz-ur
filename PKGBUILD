# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer: George Rawlinson
# Contributor: Pekka Ristola <pekkarr [at] protonmail [dot] com>
# Contributor: Caltlgin Stsodaat <contact@fossdaily.xyz>

_os="$( \
  uname \
    -o)"
_py="python"
_pkg=rapidfuzz
pkgname="${_py}-${_pkg}"
pkgver=3.6.2
pkgrel=3
pkgdesc='Rapid fuzzy string matching in Python using various string metrics'
arch=(
  'x86_64'
  'aarch64'
  'arm'
  'i686'
  'pentium4'
  'armv7l'
  'mips'
  'powerpc'
)
_http="https://github.com"
_ns="maxbachmann"
url="${_http}/${_ns}/${_pkg}"
license=(
  'MIT'
)
depends=(
  "${_py}"
)
[[ "${_os}" == "Android" ]] && \
  depends+=(
    'ndk-sysroot'
  )
[[ "${_os}" == "Android" ]] && \
  depends+=(
    'gcc-libs'
    'glibc'
  )
makedepends=(
  'cython'
  'git'
  "${_py}-build"
  "${_py}-installer"
  "${_py}-scikit-build"
  'rapidfuzz-cpp'
)
checkdepends=(
  "${_py}-hypothesis"
  "${_py}-pandas"
  "${_py}-pytest"
)
optdepends=(
  "${_py}-numpy"
)
_commit='26917be34e943fd082f13f3106b84b4c27a7f56a'
source=(
  "${pkgname}::git+${url}#commit=${_commit}"
  'github.com-taskflow-taskflow::git+https://github.com/taskflow/taskflow'
)
b2sums=(
  'SKIP'
  'SKIP'
)

pkgver() {
  cd \
    "$pkgname"
  git \
    describe \
      --tags | \
    sed \
      's/^v//'
}

prepare() {
  cd \
    "$pkgname"
  # prepare git submodules
  git \
    submodule \
      init \
        extern/taskflow
  git \
    config \
      submodule.extern/taskflow.url \
      "${srcdir}/github.com-taskflow-taskflow"
  git \
    -c \
      protocol.file.allow=always \
    submodule \
      update
}

build() {
  local \
    _ldflags=()
  cd \
    "${pkgname}"
  _ldflags+=(
    "${LDFLAGS}"
    '-fuse-ld=lld'
  )
  export \
    LDFLAGS="${_ldflags[*]}"
  LDFLAGS="${_ldflags[*]}" \
  RAPIDFUZZ_BUILD_EXTENSION=1 \
  "${_py}" \
    -m \
      build \
    --wheel \
    --no-isolation
}

check() {
  cd \
    "$pkgname"
  "${_py}" \
    -m \
     kvenv \
    --system-site-packages \
    test-env
  "test-env/bin/${_py}" \
    -m \
      installer \
    dist/*.whl
  "test-env/bin/${_py}" \
    -m \
      pytest
}

package() {
  local \
    _site_packages
  cd \
    "$pkgname"
  "${_py}" \
    -m \
      installer \
    --destdir="${pkgdir}" \
    dist/*.whl
  # documentation
  install \
    -vDm644 \
    -t \
    "${pkgdir}/usr/share/doc/$pkgname" \
    README.md
  # symlink license file
  _site_packages=$( \
    "${_py}" \
       -c \
         "import site; print(site.getsitepackages()[0])")
  install \
    -d \
    "${pkgdir}/usr/share/licenses/${pkgname}"
  ln \
    -s \
    "${_site_packages}/${_pkg}-${pkgver}.dist-info/LICENSE" \
    "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}

