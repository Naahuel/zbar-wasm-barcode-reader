# Using the ZBar C library in the web with WebAssembly
This is a repo containing the code from the article "USING THE ZBAR BARCODE SCANNING SUITE IN THE BROWSER WITH WEBASSEMBLY" from "BARKEY WOLF CONSULTING"

## Instructions
1. Install Emscripten first
https://emscripten.org/docs/getting_started/downloads.html

2. Install ZBar build tools
* GNU autoconf 2.61
* GNU automake 1.10.1
* GNU libtool 2.2.6
* GNU gettext 0.18.1.1
* GNU pkg-config 0.25

3. Clone the latest ZBar code from the Github repo
`git clone git@github.com:ZBar/ZBar.git`

4. Or even better (more updated)
`git clone git://linuxtv.org/zbar.git`

5. cd into the directory
`cd ZBar`

6. Delete all -Werror strings from configure.ac
```
# Don't treat warnings as errors!
sed -i "s/ -Werror//" $(pwd)/configure.ac
```

7. Generate automake files
`autoreconf -i`

8. Configure: disable all unneccesary features
This may produce red error messages, but it is safe to ignore them (it assumes that emscripten is GCC and uses invalid parameters on it)
```
emconfigure ./configure --without-x --without-jpeg --without-imagemagick --without-npapi --without-gtk --without-python --without-qt --without-xshm --disable-video --disable-pthread --enable-codes=all
```

9. Compile ZBar
`emmake make`

10. Compile WASM
```
cd ..
emcc -O3 -s WASM=1 \
--js-library ./library.js \
-s EXTRA_EXPORTED_RUNTIME_METHODS='["cwrap", "Pointer_stringify"]' \
-I `pwd`/ZBar/include ./scan.c ./ZBar/zbar/.libs/libzbar.a
```
