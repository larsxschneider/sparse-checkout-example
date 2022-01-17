# Sparse checkout example

This repository is an artificial example for a large mono repository. The mono repository has two components A and B. A has lots of large files/binary content and B has only source code. Teams that are only working on component B usually don't want to download all the data.

Therefore, this repository introduces a [`bootstrap` branch](../../tree/bootstrap) with just a [`run` script](../../blob/bootstrap/run). The bootstrap branch can be cloned quickly and the [`run` script](../../blob/bootstrap/run) initializes the tree for the respective teams using Git sparse checkout. This way only the necessary amount of data is transferred. In addition, the [`run` script](../../blob/bootstrap/run) configures the local Git installation in an optimal way and ensures a recent Git version.

## Usage

In order to make use of the [`run` script](../../blob/bootstrap/run), clone the repository with only the [`bootstrap` branch](../../tree/bootstrap):
```
$ git clone --filter=blob:none -b bootstrap git@github.com:larsxschneider/sparse-checkout-example.git
```

Afterwards invoke the [`run` script](../../blob/bootstrap/run):
```
$ ./sparse-checkout-example/run
```

## Examples

### Example 1: Normal clone

A normal clone of the repository transfers ~50 MB of data and takes ~15 seconds with the given network connection:
```
$ time git clone git@github.com:larsxschneider/sparse-checkout-example.git

Cloning into 'sparse-checkout-example'...
remote: Enumerating objects: 21, done.
remote: Counting objects: 100% (21/21), done.
remote: Compressing objects: 100% (12/12), done.
remote: Total 21 (delta 2), reused 17 (delta 1), pack-reused 0
Receiving objects: 100% (21/21), 47.70 MiB | 3.42 MiB/s, done.
Resolving deltas: 100% (2/2), done.
git clone git@github.com:larsxschneider/sparse-checkout-example.git  0.24s user 0.29s system 3% cpu 14.772 total
```

### Example 2: Filter clone with bootstrap script:

A clone of the bootstrap branch without blobs transfers ~0.003 MB of data and takes ~2 seconds with the given network connection:
```
$ time git clone --filter=blob:none -b bootstrap git@github.com:larsxschneider/sparse-checkout-example.git

Cloning into 'sparse-checkout-example'...
remote: Enumerating objects: 12, done.
remote: Counting objects: 100% (12/12), done.
remote: Compressing objects: 100% (9/9), done.
Receiving objects: 100% (12/12), done.
Resolving deltas: 100% (2/2), done.
remote: Total 12 (delta 2), reused 10 (delta 1), pack-reused 0
remote: Enumerating objects: 1, done.
remote: Counting objects: 100% (1/1), done.
remote: Total 1 (delta 0), reused 1 (delta 0), pack-reused 0
Receiving objects: 100% (1/1), 2.47 KiB | 2.47 MiB/s, done.
git clone --filter=blob:none -b bootstrap   0.02s user 0.08s system 7% cpu 1.360 total
```

Afterwards we could checkout just the smallish `component B` which would only take 6 more seconds:
```
./sparse-checkout-example/run

##############################################################
     Checking Git and Git LFS...
##############################################################
Git version:     2.34.1
Git LFS version: 2.13.3

Git user:  John Doe
Git email: jdoe@github.com

##############################################################
     Bootstrapping repository...
##############################################################

##############################################################
     What components of the repository do you want?
##############################################################
1: ALL
2: Component A
3: Component B

?: 3
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Total 5 (delta 0), reused 3 (delta 0), pack-reused 0
Receiving objects: 100% (5/5), 745 bytes | 745.00 KiB/s, done.

##############################################################
     Hurray! Your Git repository is ready for you!
##############################################################
Bootstrap time: 6 s
```

If we checkout the bigger `component A` then more data is transferred and it takes longer (10 seconds):
```
./sparse-checkout-example/run

##############################################################
     Checking Git and Git LFS...
##############################################################
Git version:     2.34.1
Git LFS version: 2.13.3

Git user:  John Doe
Git email: jdoe@github.com

##############################################################
     Bootstrapping repository...
##############################################################

##############################################################
     What components of the repository do you want?
##############################################################
1: ALL
2: Component A
3: Component B

?: 2
remote: Enumerating objects: 3, done.
remote: Counting objects: 100% (3/3), done.
remote: Total 3 (delta 0), reused 3 (delta 0), pack-reused 0
Receiving objects: 100% (3/3), 47.70 MiB | 8.63 MiB/s, done.

##############################################################
     Hurray! Your Git repository is ready for you!
##############################################################
Bootstrap time: 10 s
```
