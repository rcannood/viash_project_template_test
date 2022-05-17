Viash Project Template
================

<!-- NOTE! README.md was built with quarto using the command 'quarto render README.qmd' -->
<p align="center">
<a href="https://viash.io/">
<img alt="viash" src="https://viash.io/logo/viash_large.svg" width="300">
</a>
</p>

This repository is a template for setting up new Viash projects.

## First development build

After forking it, run the following commands to build your first
development build of the pipeline.

``` sh
# install viash and nextflow
bin/init

# build components
bin/viash_build

# run pipeline
bin/nextflow run . \
  -main-script workflows/my_pipeline/main.nf \
  -with-docker \
  --input resources/file*.tsv \
  --publishDir temp
```

## Unit testing

``` sh
bin/viash_test
```

## Building a release

The script below assumes you have a separate clone of this repository
specifically for building a release. To do so, switch to the release
branch, merge all changes from the main branch, build all components and
push the new release to Github and the containers to your Docker
registry of choice.

``` sh
# switch to release branch
git checkout release

# allow publishing the target folder
sed -i '/^target$/d' .gitignore

TAG=0.1.0

rm -r target
git fetch origin
git merge origin/main

# build target folder and docker containers
bin/viash_build -m release -t $TAG --max_threads 20

# run unit tests (ideally, these should all pass)
bin/viash_test -m release -t $TAG

# push docker containers to container registry
bin/viash_push -m release -t $TAG --max_threads 20

# commit current code to release branch
git add target
git commit -m "Release $TAG"
git push 

# create new tag
git tag -a "$TAG" -m "Release $TAG"
git push --tags
```

## Running production version

When a release has been made, you can run the pipeline as follows:

``` sh
bin/nextflow run https://github.com/viash-io/viash_project_template \
  -main-script workflows/my_pipeline/main.nf \
  -resume -latest \
  -with-docker \
  --input resources/file*.tsv \
  --publishDir temp
```

## Documentation

The viash documentation is available online at
[`viash.io`](https://viash.io).

## License

Copyright (C) 2020 Data Intuitive

This program is free software: you can redistribute it and/or modify it
under the terms of the GNU General Public License as published by the
Free Software Foundation, either version 3 of the License, or (at your
option) any later version.

This program is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General
Public License for more details.

You should have received a copy of the GNU General Public License along
with this program. If not, see <http://www.gnu.org/licenses/>.
