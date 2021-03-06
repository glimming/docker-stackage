# Haskell Stackage Docker image

This image is usable for building docker images with Haskell binaries.

Using double image trick, we build the binary using this image, but package into another.

Create `build.sh`:

```sh
#!/bin/sh
set -exu
cd /build
rm -rf .cabal-sandbox .cabal.sanbox.config
cabal clean
rm -f cabal.config                 # stackage image has global constraints
cabal install --only-dependencies
cabal configure
cabal build
chmod -R a+rw dist                 # we build as root, have to make everything rw
```

and `Dockerfile`

```
FROM        snoyberg/haskell-scratch
COPY        dist/build/yourapp/yourapp /yourcompany/yourapp
ENTRYPOINT  [ "/yourcompany/yourapp" ]
```

Then we can create an image without build dependencies:

```
docker run --rm=true -v $(pwd):/build phadej/stackage:2.9 sh /build/build.sh  # build binary
docker build .                                                                # build image
```

--

This image contains some packages already compiled (my subjective picks)
