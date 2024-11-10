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
_pip='false'
_build='true'
[[ "${_os}" == "Android" ]] && \
  _pip='true'
  _build='false'
_py="python"
_pyver="$( \
  "${_py}" \
    -V | \
    awk \
      '{print $2}')"
_pymajver="${_pyver%.*}"
_pyminver="${_pymajver#*.}"
_pynextver="${_pymajver%.*}.$(( \
  ${_pyminver} + 1))"
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
  "${_py}>=${_pymajver}"
  "${_py}<${_pynextver}"
  "${_py}-importlib-metadata"
)
[[ "${_os}" == "Android" ]] && \
  depends+=(
    'ndk-sysroot'
    # 'binutils-gold'
    # 'llvmgold'
  )
[[ "${_os}" == "GNU/Linux" ]] && \
  depends+=(
    'gcc-libs'
    'glibc'
  )
makedepends=(
  'cython'
  'git'
  "${_py}-scikit-build"
  'rapidfuzz-cpp'
)
if [[ "${_pip}" == "true" ]]; then
  makedepends+=(
    "${_py}-pip"
  )
elif [[ "${_build}" == "true" ]]; then
  makedepends+=(
    "${_py}-build"
    "${_py}-installer"
  )
fi
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
  "github.com-taskflow-taskflow::git+${_http}/taskflow/taskflow"
)
b2sums=(
  'SKIP'
  'SKIP'
)

pkgver() {
  cd \
    "${pkgname}"
  git \
    describe \
      --tags | \
    sed \
      's/^v//'
}

prepare() {
  cd \
    "${pkgname}"
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
  echo \
    "add_link_options('-fuse-ld=lld')" >> \
    "boh"
}

build() {
  local \
    _ldflags=()
  cd \
    "${pkgname}"
  if [[ "${_os}" == "Android" ]]; then
    _ldflags+=(
      "${LDFLAGS}"
      '-fuse-ld=lld'
    )
    CMAKE_CXX_FLAGS="-fuse-ld=lld"
    export \
      CC="cc" \
      CXX="clang++" \
      CMAKE_CXX_FLAGS="-fuse-ld=lld" \
      LDFLAGS="${_ldflags[*]}" \
      # RAPIDFUZZ_BUILD_EXTENSION=1
  fi
  # CMAKE_CXX_FLAGS="-fuse-ld=lld" \
  # LDFLAGS="${_ldflags[*]}" \
  if [[ "${_build}" == "true" ]]; then
    # RAPIDFUZZ_BUILD_EXTENSION=1 \
    "${_py}" \
      -m \
        build \
      --wheel \
      --no-isolation
  fi || \
    true
}

check() {
  cd \
    "${pkgname}"
  "${_py}" \
    -m \
      venv \
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
    _site_packages \
    _python_version
  _python_version="$( \
    "${_py}" \
      -c \
     'import sys; print(".".join(map(str, sys.version_info[:2])))')"
  _site_packages=$( \
    "${_py}" \
       -c \
         "import site; print(site.getsitepackages()[0])")
  cd \
    "${pkgname}"
  if [[ "${_pip}" == "true" ]]; then
    pip \
      install \
      . \
      --root="${pkgdir}"
      # --strip-file-prefix="${pkgdir}"
    tree \
      "${pkgdir}/${_site_packages}/${_pkg}"
    if [[ "${_pip}" == "true" ]]; then
      mkdir \
        -p \
        "${pkgdir}/usr/lib/python${_python_version}/site-packages"
      mv \
        "${pkgdir}/${_site_packages}/"* \
        "${pkgdir}/usr/lib/python${_python_version}/site-packages" || \
        true
    fi || \
      true
  elif [[ "${_build}" == "true" ]]; then
    "${_py}" \
      -m \
        installer \
      --destdir="${pkgdir}" \
      dist/*.whl
  fi
  # documentation
  install \
    -vDm644 \
    -t \
    "${pkgdir}/usr/share/doc/${pkgname}" \
    README.md
  install \
    -d \
    "${pkgdir}/usr/share/licenses/${pkgname}"
  ln \
    -s \
    "${_site_packages}/${_pkg}-${pkgver}.dist-info/LICENSE" \
    "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}

