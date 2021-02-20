# Maintainer: Ross Clark <archiv8@artisteducator.com>
# Contributor: Ross Clark <archiv8@artisteducator.com>

_relname="eslint"
_langname="nodejs"

# pkgbase=
pkgname="${_langname}-${_relname}"
pkgver=7.2.0
pkgrel=1
#epoch=
pkgdesc="An AST-based pattern checker for JavaScript"
arch=("any")
url="https://eslint.org"
license=("MIT")
# groups
depends=("nodejs")
# optdepends=()
makedepends=("npm" "jq")
# checkdepends=()
# provides=()
# conflicts=()
# replaces=()
# backup=()
# options=()
# install=
changelog="CHANGELOG.md"
source=(
"https://registry.npmjs.org/$_relname/-/$_relname-$pkgver.tgz"
"CHANGELOG.md"
"README.md"
)
noextract=("$_relname-$pkgver.tgz")
# validpgpkeys=()
sha512sums=('07706d132683282e4c95f0dad876bcfc3e83b12e1f8eef79cecd218d2dc77466b3c3e2cd6b26a2dfc036e6a329a4adfbc1619635848f391ea8633973e4c1ec45'
            '83e622fd705b5c92f2c3cbba52788ccbb380d17c5f6043c98280da75e0f5c7a2f9cb104a408c9c2ca9fd6a87005810dbdbbccc2ef5201cc343cd7cf0d0235125'
            '4c895ce922a488c8b34a69b2a1e3476900b7728810f637f46e08aede0a14ea594da79574d8acf52fe1523f2218d7a5141df4cf5dc82b0b817a12a4b1ca9d747f')
package() {
  # Ensure cache is set when install is run in order to avoid littering user's home directory
printf "\e[1;32m==>\e[0m \e[38;5;248mBuilding nodejs package... \e[0m\n"
printf "    This may take some time depending on the size of the package and number of dependencies to download... \e[0m\n"
npm install --cache "$srcdir/npm-cache" -g --user root --prefix "$pkgdir"/usr "$srcdir"/$_relname-$pkgver.tgz

# Fix incorrect user / group as highlighted by namcap
printf "\e[1;32m==>\e[0m \e[38;5;248mCorrecting possible ownership issues\e[0m\n"
while IFS= read -r fsitem; do
  chown -h root:root "$fsitem"
done <   <(find "$pkgdir" -not -group root -not -user root)

# Non-deterministic race in npm gives 777 permissions to random directories.
# See https://github.com/npm/npm/issues/9359 for details.
printf "\e[1;32m==>\e[0m \e[38;5;248mCorrecting possible permission issues on directories\e[0m\n"
find "$pkgdir/usr" -type d -exec chmod 755 '{}' +

# Remove references to $pkgdir
printf "\e[1;32m==>\e[0m \e[38;5;248mRemoving references to \$pkgdir\e[0m\n"
find "$pkgdir" -type f -name package.json -print0 | xargs -0 sed -i "/_where/d"

# Remove references to $srcdir
printf "\e[1;32m==>\e[0m \e[38;5;248mRemoving references to \$srcdir \e[0m\n"
local tmppackage="$(mktemp)"
local pkgjson="$pkgdir/usr/lib/node_modules/$_relname/package.json"
jq '.|=with_entries(select(.key|test("_.+")|not))' "$pkgjson" > "$tmppackage"
mv "$tmppackage" "$pkgjson"
chmod 644 "$pkgjson"

# Install license
install -dm755 "${pkgdir}/usr/share/licenses/${pkgname}"
ln -s ../../../lib/node_modules/eslint/LICENSE "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"

# Create Archiv8 documentation folder
install -dvm 755 "$pkgdir/usr/share/doc/$pkgname/packaging/"

# Install Archiv8 Documentation
install -Dm 644 "CHANGELOG.md" "$pkgdir/usr/share/doc/$pkgname/packaging/CHANGELOG.md"
install -Dm 644 "README.md" "$pkgdir/usr/share/doc/$pkgname/packaging/README.md"
}
