# Dune vendoring and libraries

Vendored libraries can by default be picked by dune in release mode. I.e. running `dune build -p a`
is allowed to build other local packages if they are in directories marked as vendored by a
`(vendored_dirs ...)` stanza.

This can lead to surprising results for users that vendor code for development but do not wish to
bundle their dependencies.

This repository contains a reproduction of such unexpected behaviour. It contains 2 local packages
`a` and `b` and a third `c` packages in the `vendors/` directory which is marked as a
`vendored_dir`.

`a` depends on `b` and `c` and `b` depends on `c`. Those dependencies are
expressed both in dune and opam files.

```
git clone https://github.com/samoht/vendors-test.git
cd vendors-test
opam switch create ./ 4.11.1
opam pin --no-action vendors/c
opam pin ./
```

This will fail because dune will find a conflict between the `c` installed in opam and the one
that's vendored:

```
Error: Conflict between the following libraries:
- "c" in _build/default/vendors/c
- "c" in /home/nathan/code/vendors-test/_opam/lib/c
  -> required by library "b" in /home/nathan/cod
```
