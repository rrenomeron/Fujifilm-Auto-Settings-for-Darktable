# Fujifilm Auto Settings for darktable

This repository contains styles and scripts and LUTs to automatically
read and apply

- Film Simulation
- DR100/DR200/DR400 mode

It is a fork of [Bastian Bechtold's Fujifilm Auto Settings Script
|https://github.com/bastibe/Fujifilm-Auto-Settings-for-Darktable]. This version uses ``exiv2``
instead of ``exiftool`` so that it will function in the Flatpak version of darktable.

As a consequence of switching to ``exiv2`` we are currently unable to apply the aspect ratio
(crop) setting from because ``exiv2`` can't read the appropriate EXIF value.

## Installation

Install `exiv2` v0.28.3 or later and make sure it is available on `$PATH`.  If you are
using the flatpak, you don't need to do anything, as the ``exiv2`` command line tool is
installed in the flatpak container.

Go to darktable's settings, in the tab "processing", set a 3D lut root
folder. Copy the supplied LUTs into that directory, such that e.g. the
Provia LUT is at `$3DLUTROOTFOLDER/Fuji XTrans V8/provia.png`.

Import the styles in the `styles` subdirectory. The film simulation
styles rely on the LUTs installed in the previous step.

Activate darktable's script manager from the Lighttable view in the
bottom left.

Copy `fujifilm_auto_settings.lua` to
`~/.config/darktable/lua/contrib/`, then start it with the script
manager. It should now automatically apply the styles to any imported
RAF image.

## Debugging

Start darktable with `darktable -d lua` to debug. You can run the lua
script manually by binding a keyboard shortcut to
`fujifilm_auto_settings` in darktable's settings.

## How it Works

The lua plugin calls `exiv2` to read the film simulation and DR mode from the RAF file. It then applies one of the
supplied styles, and sets an appropriate tag.

#### Film Simulations

The following styles apply Fuji film simulations from
https://github.com/bastibe/LUT-Maker:

- acros
- acros\_green
- acros\_red
- acros\_yellow
- astia
- classic\_chrome
- eterna
- mono
- mono\_green
- mono\_red
- mono\_yellow
- pro\_neg\_high
- pro\_neg\_standard
- provia
- sepia
- velvia

These styles activate *LUT 3D*, and set the appropriate LUT.

#### DR Mode

The following styles apply for DR200 and DR400:

- DR200
- DR400

As far as we can tell, the DR modes reduce the raw exposure by one/two
stops to make room for additional highlights, and increase the tone
curve for midtones and shadows to compensate.

The supplied styles implement this using the tone equalizer, by
raising the -8 EV to -4 EV sliders to +1 EV, then -3 EV to +0.75, -2
EV to +0.5, -1 EV to +0.25 EV, and 0 EV to 0 EV (for DR200; double all
values for DR400). We experimented a bit with various *preserve
details* functions, and found *eigf* to look most similar to Fuji's
embedded JPEGs, so that's what the styles use.

Of course this can only work for properly exposed images, and even
then might not be perfectly reliable. But it usually gets the images
in the right ballpark in our testing.

## Changelog

2025-xx-xx Refactor to use exiv2 instead of exiftool, so it will work with flatpak
2022-07-20 Only call exiftool once, to speed up operation  
2022-07-20 Added monochrome LUTs and styles  
2022-07-20 Various fixes by Teh-Lemon  
2022-07-17 Updated LUTs with correct indexing, for markedly improved colors.

## Known Issues

The LUTs training data does not contain saturated colors beyond what
is natural. Even though some intelligent extrapolation is applied,
pushing saturation too far may result in incorrect colors. Apply a
parametric mask that excludes high Cz if this becomes a problem.

In particular, oversaturated colors will turn grey in the sepia LUT.

## License

These scripts and LUTs are provided under the terms of the GPL license
