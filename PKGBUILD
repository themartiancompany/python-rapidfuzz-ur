# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer: George Rawlinson
# Contributor: Pekka Ristola <pekkarr [at] protonmail [dot] com>
# Contributor: Caltlgin Stsodaat <contact@fossdaily.xyz>

pkgname=python-rapidfuzz
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
url='https://github.com/maxbachmann/rapidfuzz'
license=(
  'MIT'
)
depends=(
  'glibc'
  'gcc-libs'
  'python'
)
makedepends=(
  'git'
  'python-build'
  'python-installer'
  'cython'
  'python-scikit-build'
  'rapidfuzz-cpp'
)
checkdepends=(
  'python-hypothesis'
  'python-pandas'
  'python-pytest'
)
optdepends=(
  'python-numpy'
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
  cd "$pkgname"

  RAPIDFUZZ_BUILD_EXTENSION=1 \
    python -m build --wheel --no-isolation
}

check() {
  cd "$pkgname"

  python -m venv --system-site-packages test-env
  test-env/bin/python -m installer dist/*.whl
  test-env/bin/python -m pytest
}

package() {
  cd \
    "$pkgname"
  python \
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
  local site_packages=$(python -c "import site; print(site.getsitepackages()[0])")
  install -d "$pkgdir/usr/share/licenses/$pkgname"
  ln -s "$site_packages/${pkgname#python-}-$pkgver.dist-info/LICENSE" \
    "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}

