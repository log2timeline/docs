The log2timeline projects distribute binary packages for macOS and Windows via 
the [l2tbinaries](https://github.com/log2timeline/l2tbinaries) git repository.

l2tbinaries comes with 3 branches:

* testing; track intended for testing changes to binary packages;
* dev; development track intended for the development release of Plaso and automated testing on GitHub and AppVeyor;
* master; stable track intended for the latest release of plaso.


### Building macOS package files

To build macOS package files use [l2tdevtools](https://github.com/log2timeline/l2tdevtools)
with build target: [pkg](https://github.com/log2timeline/l2tdevtools/wiki/Build-script#build-target-pkg).

Alternatively build a macOS package dpkg file manually with:

```
tar xfvz dfvfs-20140219.tar.gz
cd dfvfs-20140219/
python setup.py install --root=$PWD/tmp --install-data=/usr/local
pkgbuild --root tmp --identifier com.github.log2timeline.dfvfs --version 20140219 --ownership recommended python-dfvfs-20140219.pkg
cd ..
```

### Building Windows package files

To build Windows package files use [l2tdevtools](https://github.com/log2timeline/l2tdevtools)
with build target: [msi](https://github.com/log2timeline/l2tdevtools/wiki/Build-script#build-target-msi).

Alternatively build a Windows package MSI file manually with:

```
tar xfvz dfvfs-20140219.tar.gz
cd dfvfs-20140219/
python setup.py bdist_msi
cd ..
```

### Uploading binary package files to l2tbinaries

**Always upload to the testing branch first**

```
git checkout testing
git reset --hard 0f5dcf092ca43c3be88533b4d3fbdc1287bae948
git pull
```

To upload to the testing branch of l2tbinaries:
```
./utils/macos-sync.sh
```

Remove any unwanted updates.

```
git commit -a -m "Updated macOS builds of dependencies."
git push
```

### Promoting binary package files from testing to dev

#### Preparing testing

Update the SHA-256 integrity hashes

```
./utils/update-sha256sums.sh
```

Note: that this script will switch to the testing branch and reorder the commits in the following order (from newest to oldest):

```
Updated 64-bit Windows builds of dependencies.
Updated 32-bit Windows builds of dependencies.
Updated macOS builds of dependencies.
Worked on tests.
Worked on utility scripts.
Updated README.md
Initial commit.
```

Use `git rebase -i` to squash merge similar commits.

```
git rebase -i 0f5dcf092ca43c3be88533b4d3fbdc1287bae948
git push -f
```

Wait for the GitHub actions and AppVeyor tests to pass.

#### Updating dev branch

To update dev from testing


```
./utils/update-dev.sh
```

### Promoting binary package files from dev to master (stable)

This process is much the same as updating dev from testing:

```
./utils/update-stable.sh
```
