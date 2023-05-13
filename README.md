# IPreader

IPreader is a program for the data conversion of powder X-ray diffraction data
from a Guinier camera in form of an image (tiff, gel, png) to an xy or raw
file. Typical applications are processing of images from scanned imaging-plates
or scanned conventional silver-based films.

## Fedora

```sh
dnf install tcl-devel tk tkimg tcllib cmake
cmake -B build
cmake --build build
mv build/tiffread1.so .
```

