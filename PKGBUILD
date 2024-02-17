# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: George Rawlinson <grawlinson@archlinux.org>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Dan McGee <dan@archlinux.org>
# Contributor: Daniele Paolella <dp@mcrservice.it>
# Maintainer:  Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer:  Truocolo <truocolo@aol.com>

_os="$( \
  uname -o)"
_py="python2"
_pkg="virtualenv"
pkgname="${_py}-${_pkg}"
pkgver=20.25.0
pkgrel=1
pkgdesc='Virtual Python Environment builder'
arch=(
  'any'
)
url="https://${_pkg}.pypa.io"
license=(
  'MIT'
)
depends=(
  "${_py}"
  "${_py}-distlib"
  "${_py}-filelock"
  "${_py}-platformdirs"
)
makedepends=(
  'git'
  'python-filelock'
  'python-hatchling'
  'python-hatch-vcs'
  'python-installer'
  'python-sphinx'
  'python-sphinx-argparse'
  'python-sphinxcontrib-towncrier'
  'python-wheel'
  'towncrier'
)
checkdepends=(
  'fish'
  'python-coverage'
  'python-flaky'
  'python-pip'
  'python-pytest'
  'python-pytest-freezer'
  'python-pytest-mock'
  'python-time-machine'
  'tcsh'
  'xonsh'
)
replaces=(
  'virtualenv'
)
conflicts=(
  'virtualenv'
)
options=(
  '!makeflags'
)
_commit='1941c1d5abf81814992b68bbc86c0020dc75a3ad'
source=(
  "${pkgname}::git+https://github.com/pypa/virtualenv#commit=${_commit}")
b2sums=(
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

build() {
  cd \
    "$pkgname"

  "${_py}" \
    -m \
      wheel \
        pack
  #   -m \
  #     build \
  #       --wheel \
  #       --no-isolation
  # NOTE: install to tmp dir for documentation and tests
  # python \
  #   -m \
  #     installer \
  #       --destdir=test_dir \
  "${_py}" \
    -m \
      wheel \
        unpack \
          --dest="test_dir" \
          dist/*.whl
  local \
    site_packages=$( \
    python \
      -c \
        "import site; print(site.getsitepackages()[0])")
  PYTHONPATH="$(pwd)/test_dir/${site_packages}:${PYTHONPATH}" \
    sphinx-build \
      -b \
      man \
      docs \
      docs/_build/man
}

check() {
  local \
    pytest_options=()
  pytest_options=(
    -vv
    # tests try to find python2
    --deselect tests/unit/create/test_creator.py::test_py_pyc_missing[True-False]
    --deselect tests/unit/create/test_creator.py::test_py_pyc_missing[False-False]
    --deselect tests/unit/discovery/py_info/test_py_info.py::test_fallback_existent_system_executable
    --deselect tests/unit/test_util.py::test_reentrant_file_lock_is_thread_safe
  )
  local \
    site_packages=$( \
    python \
      -c \
        "import site; print(site.getsitepackages()[0])")
  cd \
    "$pkgname"
  PYTHONPATH="$(pwd)/test_dir/${site_packages}:${PYTHONPATH}" \
    pytest \
      "${pytest_options[@]}"
}

package() {
  local \
    _ln_opts=()
  cd \
    "${pkgname}"
  "${_py}" \
    -m \
      wheel \
        unpack \
          --dest="test_dir" \
          dist/*.whl
  # python \
  #   -m \
  #     installer \
  #     --destdir="${pkgdir}" \
  #     dist/*.whl
  # man page
  install \
    -vDm644 \
    -t \
    "${pkgdir}/usr/share/man/man1" \
    docs/_build/man/virtualenv.1
  # sort out files with suffix of 3
  ln \
    -s \
    virtualenv.1.gz \
    "${pkgdir}/usr/share/man/man1/virtualenv3.1.gz"
  [[ "${_os}" != 'Linux' ]] && \
    _ln_opts=(
      -s
    )
  mv \
    "$pkgdir/usr/bin/virtualenv" \
    "$pkgdir/usr/bin/virtualenv2"
  # symlink license file
  local \
    site_packages=$( \
      python \
        -c \
	  "import site; print(site.getsitepackages()[0])")
  install \
    -d \
      "$pkgdir/usr/share/licenses/$pkgname"
  ln \
    -s \
    "$site_packages/${_pkg}-$pkgver.dist-info/licenses/LICENSE" \
    "$pkgdir/usr/share/licenses/$pkgname/LICENSE"
}

# vim:set sw=2 sts=-1 et:
