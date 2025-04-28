## Issue with submodule shallow checkout size when default branch in github is different from branch specified in .gitmodules

I have two repos:
  - https://github.com/pps83/submodule-test-bad
  - https://github.com/pps83/submodule-test-ok

`submodule-test-bad` adds https://github.com/pps83/brotli-master as submodule with shallow checkout of `master-min` branch.

`submodule-test-ok` adds https://github.com/pps83/brotli-master-min as submodule with shallow checkout of `master-min` branch.

[brotli-master](https://github.com/pps83/brotli-master) and [brotli-master-min](https://github.com/pps83/brotli-master-min) are identical repos, with only small difference: `brotli-master` is configured on github to have `master` as default branch, while `brotli-master-min` is configured on github to have `master-min` as default branch. It's irrelevant to the issue, but for understanding: branch `master-min` has only one commit on top of branch `master`. This one commit removes unneded stuff, basically deletes 95% by size of that brotli repo.

Effectivelly, when `submodule-test-bad` and `submodule-test-ok` are checkout, they should be identical, as both of them add the same submodule, since `master-min` branch of `brotli-master` (hash [1e6f9b](https://github.com/pps83/brotli-master/commit/1e6f9b4b4c98f6f99ba9a860cbb982346631df80)) is identical to `master-min` branch of `brotli-master-min` (the same hash [1e6f9b](https://github.com/pps83/brotli-master-min/commit/1e6f9b4b4c98f6f99ba9a860cbb982346631df80)).


I use this command to checkout `submodule-test-bad`:
```
git clone https://github.com/pps83/submodule-test-bad.git
cd submodule-test-bad
time git submodule update --progress --init --force
```
and this command to checkout `submodule-test-ok`:
```
git clone https://github.com/pps83/submodule-test-ok.git
cd submodule-test-ok
time git submodule update --progress --init --force
```

When both are checked out, these two have to have identical brotli submodule. This works as expected, and `diff` doesn't find any differences:

```
diff -r submodule-test-bad/brotli submodule-test-ok/brotli
````

## Problem

The issue, however, is that takes 4min20sec to checkout submodule-test-bad, while it takes only 3 seconds for `submodule-test-ok`!


This is the output for `submodule-test-bad` checkout:
```
Cloning into 'submodule-test-bad'...
remote: Enumerating objects: 12, done.
remote: Counting objects: 100% (12/12), done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 12 (delta 2), reused 5 (delta 0), pack-reused 0 (from 0)
Receiving objects: 100% (12/12), done.
Resolving deltas: 100% (2/2), done.
Submodule 'brotli' (https://github.com/pps83/brotli-master.git) registered for path 'brotli'
Cloning into '/d/work-pps/submodule-test/submodule-test-bad/brotli'...
remote: Enumerating objects: 414, done.
remote: Counting objects: 100% (414/414), done.
remote: Compressing objects: 100% (352/352), done.
remote: Total 414 (delta 45), reused 217 (delta 23), pack-reused 0 (from 0)
Receiving objects: 100% (414/414), 31.58 MiB | 126.00 KiB/s, done.
Resolving deltas: 100% (45/45), done.
remote: Enumerating objects: 7, done.
remote: Counting objects: 100% (7/7), done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 4 (delta 3), reused 4 (delta 3), pack-reused 0 (from 0)
Unpacking objects: 100% (4/4), 363 bytes | 21.00 KiB/s, done.
From https://github.com/pps83/brotli-master
 * branch            1e6f9b4b4c98f6f99ba9a860cbb982346631df80 -> FETCH_HEAD
Submodule path 'brotli': checked out '1e6f9b4b4c98f6f99ba9a860cbb982346631df80'

real    4m21.881s
user    0m3.814s
sys     0m4.642s
```

This is the output for `submodule-test-ok` checkout:
```
git clone https://github.com/pps83/submodule-test-ok.git
cd submodule-test-ok
time git submodule update --progress --init --force
```
it takes only 3 seconds. This is the output:
```
Cloning into 'submodule-test-ok'...
remote: Enumerating objects: 12, done.
remote: Counting objects: 100% (12/12), done.
remote: Compressing objects: 100% (7/7), done.
remote: Total 12 (delta 2), reused 12 (delta 2), pack-reused 0 (from 0)
Receiving objects: 100% (12/12), done.
Resolving deltas: 100% (2/2), done.
Submodule 'brotli' (https://github.com/pps83/brotli-master-min.git) registered for path 'brotli'
Cloning into '/d/work-pps/submodule-test/submodule-test-ok/brotli'...
remote: Enumerating objects: 109, done.
remote: Counting objects: 100% (109/109), done.
remote: Compressing objects: 100% (105/105), done.
remote: Total 109 (delta 7), reused 28 (delta 3), pack-reused 0 (from 0)
Receiving objects: 100% (109/109), 505.73 KiB | 389.00 KiB/s, done.
Resolving deltas: 100% (7/7), done.
Submodule path 'brotli': checked out '1e6f9b4b4c98f6f99ba9a860cbb982346631df80'

real    0m3.218s
user    0m0.580s
sys     0m0.444s
```

Also, size of `submodule-test-bad` is 34MB, while size of `submodule-test-ok` is only 2.9MB:
```bash
mtlro@MAIN-NOTE MSYS /d/work-pps/submodule-test
$ du -hs submodule-test-bad
34M     submodule-test-bad

mtlro@MAIN-NOTE MSYS /d/work-pps/submodule-test
$ du -hs submodule-test-ok
2.9M    submodule-test-ok
```

