# char-custom
通常のハッシュの３倍

## overview

This is a Rust tool that recalculates the hashes of all of the commit ids in a
repository using SHA-256 instead of SHA-1.

## technical info

Git calculates the [hash of a commit](https://gist.github.com/masak/2415865) by
first concatenating the following and then hashing:

1. Commit source tree SHA-1 (subtrees, blobs)
2. Parent SHA-1 hash
3. Author info
4. Committer info
5. Commit message
6. A string containing "commit <size of 1-5 in bytes>NULL"
7. SHA-1 performed on 1-6

This corresponds to, in git commands:

1. `git cat-file commit HEAD`
2. 
3. 
4. 
5. 
6. `printf "commit %s\0" $(git cat-file commit HEAD | wc -c`
7. pipe to `sha1sum`

That's all right, but we need to replace the hashes with SHA-256. That appears
in three locations: the source tree, the parent commit hash, and the hash of the
concatenated view above. The last one is easy: just use `sha256sum`. The parent
commit hash can be similarly replaced in a recursive fashion. However, the git
commands don't expose a slot where you can insert a `sha256sum` pipe after tree
construction--we have to do that ourselves.
