# cd into the mounted host directory
cd /src

# clone the latest ZBar code from the Github repo
git clone https://github.com/ZBar/ZBar

# cd into the directory
cd ZBar

# Delete all -Werror strings from configure.ac
# Don't treat warnings as errors!
sed -i "s/ -Werror//" $(pwd)/configure.ac

# Generate automake files
autoreconf -i

# Configure: disable all unneccesary features
# This may produce red error messages, but it is safe to ignore them (it assumes that
# emscripten is GCC and uses invalid parameters on it)
emconfigure ./configure --without-x --without-jpeg --without-imagemagick --without-npapi --without-gtk --without-python --without-qt --without-xshm --disable-video --disable-pthread --enable-codes=pdf417

# Compile ZBar
emmake make

# Compila WASM
emcc -O3 -s WASM=1 \
--js-library ./library.js \
-s EXTRA_EXPORTED_RUNTIME_METHODS='["cwrap", "Pointer_stringify"]' \
-I `pwd`/ZBar/include ./scan.c ./ZBar/zbar/.libs/libzbar.a