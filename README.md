## Issue with submodule shallow checkout size when default branch in github is different from branch specified in .gitmodules

I have two repos:
  - https://github.com/pps83/submodule-test-bad
  - https://github.com/pps83/submodule-test-ok

`submodule-test-bad` adds https://github.com/pps83/brotli-master as submodule with shallow checkout of `master-min` branch.

`submodule-test-ok` adds https://github.com/pps83/brotli-master-min as submodule with shallow checkout of `master-min` branch.

[brotli-master](https://github.com/pps83/brotli-master) and [brotli-master-min](https://github.com/pps83/brotli-master-min) are identical repos, with only small difference: `brotli-master` is configured on github to have `master` as default branch, while `brotli-master-min` is configured on github to have `master-min` as default branch.

Effectivelly, when `submodule-test-bad` and `submodule-test-ok` are checkout, they should be identical, as both of them add the same submodule, since `master-min` branch of `brotli-master` (hash [1e6f9b](https://github.com/pps83/brotli-master/commit/1e6f9b4b4c98f6f99ba9a860cbb982346631df80)) is identical to `master-min` branch of `brotli-master-min` (the same hash [1e6f9b](https://github.com/pps83/brotli-master-min/commit/1e6f9b4b4c98f6f99ba9a860cbb982346631df80)).
