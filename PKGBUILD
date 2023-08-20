# Maintainer: Pellegrino Prevete <pellegrinoprevete@gmail.com>
# Contributor: Alexey Pavlov <alexpux@gmail.com>
# Contributor: Ray Donnelly <mingw.android@gmail.com>
# Contributor: Duong Pham <dthpham@gmail.com>

_realname=numpy
pkgver=1.22.0
pkgbase=mingw-w64-python-${_realname}
pkgname=(
  "${MINGW_PACKAGE_PREFIX}-python-${_realname}-${pkgver}"
)
provides=(
  "${MINGW_PACKAGE_PREFIX}-python-${_realname}=1.22.0"
  "${MINGW_PACKAGE_PREFIX}-python3-${_realname}"
)
conflicts=(
  "${MINGW_PACKAGE_PREFIX}-python-${_realname}"
  "${MINGW_PACKAGE_PREFIX}-python3-${_realname}"
)
replaces=(
  "${MINGW_PACKAGE_PREFIX}-python3-${_realname}"
)
pkgrel=1
pkgdesc="Scientific tools for Python (mingw-w64)"
arch=('any')
mingw_arch=('mingw32' 'mingw64' 'ucrt64' 'clang64' 'clang32' 'clangarm64')
license=('BSD')
url="https://www.numpy.org/"
makedepends=(
  "${MINGW_PACKAGE_PREFIX}-cython"
  "${MINGW_PACKAGE_PREFIX}-python-setuptools"
  "${MINGW_PACKAGE_PREFIX}-python-pytest"
  "${MINGW_PACKAGE_PREFIX}-cc"
  $( [[ ${MINGW_PACKAGE_PREFIX} == *-clang-* ]] || \
    echo "${MINGW_PACKAGE_PREFIX}-gcc-fortran"))
depends=("${MINGW_PACKAGE_PREFIX}-python"
         "${MINGW_PACKAGE_PREFIX}-openblas")
optdepends=("${MINGW_PACKAGE_PREFIX}-python-pytest: testsuite")
#options=('!strip' 'debug')
source=(
  https://github.com/numpy/numpy/releases/download/v${pkgver}/${_realname}-${pkgver}.tar.gz 
  0001-detect-mingw-environment.patch
  0002-fix-finding-python2.patch
  0003-gfortran-better-version-check.patch
  0004-fix-testsuite.patch
  0005-mincoming-stack-boundary-32bit-optimized-64bit.patch
  0006-disable-visualcompaq-for-mingw.patch
  0007-disable-64bit-experimental-warning.patch
  0008-mingw-gcc-doesnt-support-visibility.patch
  0010-mingw-inline-stuff.patch
  0011-dont-die-if-no-fcompiler.patch
  0012-clang-no-gcc-workaround.patch)
sha256sums=(
  'f2be14ba396780a6f662b8ba1a24466c9cf18a6a386174f614668e58387a13d7'
  '94f111ab238c4a82e8613ed14e4cbeb553eabff26523f576e5fcafdbfdc8ee29'
  '3aacb1d92e7764c9f0f24afa1a97b136a069fcd81d63c9fa55565c40c5a29974'
  '2679482ce9396b551e1e1e7674f373d139b3e8a2f9729026000dd3c581de41d7'
  'a9023ca3ba0d4b444ef490ca9fa145896d24f34bb271676adf538d5da7546725'
  'fb1a42a6eed1ea0795f9c91506623153157d4410a0f06ccb76eb80457fb6e8f5'
  'fcacffaae853de592224bc51f1ecb3e617f2fc518d05373e1310aeabdf097254'
  'cafc924fd11d8653a49970d0cce5b31869cce0e8996a3ae57bcbccca96bc8eb3'
  'c7222c3cd85ff6af515514c5c3b8f3c02144c58c1373dec16683fa455504aa69'
  'ae47d0c8adbae798ca1675ae9c0a35146bad00c6b5734f713cd76c01832e5436'
  '87bdd0a47a8662bdb8acaca18452901ee1f42cf08b985443ffb214f7facfdb2d'
  '86eceee1ec86934d416c52fe8197c09c644b9f27ab93454a849e065b1ddac12f')

# Helper macros to help make tasks easier #
apply_patch_with_msg() {
  for _patch in "$@"
  do
    msg2 "Applying ${_patch}"
    patch -Nbp1 -i "${srcdir}/${_patch}"
  done
}

prepare() {
  cd ${_realname}-${pkgver}

  apply_patch_with_msg \
    0001-detect-mingw-environment.patch \
    0002-fix-finding-python2.patch \
    0003-gfortran-better-version-check.patch \
    0004-fix-testsuite.patch \
    0006-disable-visualcompaq-for-mingw.patch \
    0007-disable-64bit-experimental-warning.patch \
    0008-mingw-gcc-doesnt-support-visibility.patch \
    0010-mingw-inline-stuff.patch \
    0011-dont-die-if-no-fcompiler.patch \
    0012-clang-no-gcc-workaround.patch

  apply_patch_with_msg \
    0005-mincoming-stack-boundary-32bit-optimized-64bit.patch
    # Note, -mincoming-stack-boundary (and the other flags set) doesn't get used except
    # in a test compilation, AFAICT.

  cd ..

  cp -a ${_realname}-${pkgver} ${_realname}-py-${MSYSTEM}
}

build() {
  export ATLAS=None
  export LDFLAGS="$LDFLAGS -shared"
  # See: https://sourceforge.net/p/mingw-w64/mailman/message/36287627/
  export CFLAGS="$CFLAGS -fno-asynchronous-unwind-tables"

  local _fortran_config=""
  if [[ ${MINGW_PACKAGE_PREFIX} != *-clang-* ]]; then
    _fortran_config="config_fc --fcompiler=gnu95"
  fi

  cd ${_realname}-py-${MSYSTEM}
  MSYS2_ARG_CONV_EXCL="--prefix=;--install-scripts=;--install-platlib=" \
    ${MINGW_PREFIX}/bin/python setup.py ${_fortran_config} build
}

package() {
  _pyver=$(${MINGW_PREFIX}/bin/python -c "import sys;sys.stdout.write('.'.join(map(str, sys.version_info[:2])))")
  _pyinc=${_pyver}

  export ATLAS=None
  export LDFLAGS="$LDFLAGS -shared"
  export CFLAGS="$CFLAGS -fno-asynchronous-unwind-tables"

  local _fortran_config=""
  if [[ ${MINGW_PACKAGE_PREFIX} != *-clang-* ]]; then
    _fortran_config="config_fc --fcompiler=gnu95"
  fi

  cd ${_realname}-py-${MSYSTEM}
  MSYS2_ARG_CONV_EXCL="--prefix=;--install-scripts=;--install-platlib=" \
    ${MINGW_PREFIX}/bin/python setup.py ${_fortran_config} install --prefix=${MINGW_PREFIX} --root="${pkgdir}" --optimize=1

  install -Dm644 LICENSE.txt ${pkgdir}${MINGW_PREFIX}/share/licenses/python-${_realname}/LICENSE.txt

  install -m755 -d "${pkgdir}${MINGW_PREFIX}/include/python${_pyinc}"
  cp -rf ${pkgdir}${MINGW_PREFIX}/lib/python${_pyver}/site-packages/${_realname}/core/include/${_realname} "${pkgdir}${MINGW_PREFIX}/include/python${_pyinc}/"

  # fix python command in files
  local _mingw_prefix=$(cygpath -am ${MINGW_PREFIX})
  for _f in "${pkgdir}${MINGW_PREFIX}"/bin/*.py; do
    sed -e "s|${_mingw_prefix}/bin/|/usr/bin/env |g" -i ${_f}
  done

  # Clean *.orig files
  find "${pkgdir}${MINGW_PREFIX}" -type f -name "*.orig" -exec rm -f {} \;
}
