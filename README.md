# Patched-Stegsnow-EXE
Patched SNOW (whitespace steganography) for Windows. Fixes binary output corruption by forcing binary mode, so extracted files like 7z, zip and images come out byte-for-byte identical.
snow-windows-binary-fix

A small patch to SNOW, the whitespace steganography tool by Matthew Kwan, so it extracts binary files correctly on Windows.

The problem

SNOW hides messages in the trailing spaces and tabs at the end of lines in text files. When you use a standard Windows build to extract a binary file (7z, zip, png, exe), the output is often corrupt. Windows runs C programs in text mode by default, so every 0x0A byte gets written as 0x0D 0x0A. Any binary file with a 0x0A byte in it is damaged, and archives then fail with checksum errors.

The fix

binmode.c sets binary mode for all files and for stdin, stdout and stderr. The rest of the program is unchanged, so SNOW’s hiding and extracting behave exactly as the original does.

Usage

Extract a hidden file:

snow.exe hidden.txt output.bin

Redirecting also works in cmd.exe:

snow.exe hidden.txt > output.bin

Hide a file (no compression, no password):

snow.exe -l 100 -f secret.bin cover.txt stego.txt

Notes:

Use cmd.exe or the output-file form. Windows PowerShell 5.x re-encodes > output as UTF-16 text, which corrupts binary data.
Don’t open and save the stego text file in Notepad. Editors can change line endings and strip the trailing whitespace the data lives in.
To check an extracted file, compare its size and hash (certutil -hashfile output.bin SHA256) against the original.
Build (64-bit Windows, MinGW-w64)
x86_64-w64-mingw32-gcc -O -static -s -o snow.exe main.c encrypt.c ice.c compress.c encode.c binmode.c

On Linux or Kali, the original snow builds with make, and the stegsnow package works too (sudo apt install stegsnow).

Verification

Tested by extracting a 2,231-byte 7z archive from a text file: the output was byte-identical to the original, with the output-file form, > redirect and stdin forms. Built and tested for 64-bit only.

Licence and credits

SNOW is by Matthew Kwan and is licensed under the GPL (see COPYING). This repo contains the original source plus a small modification (binmode.c, and an added #include <string.h> in main.c). It is distributed under the same GPL licence. The original project: https://github.com/mattkwan/snow
