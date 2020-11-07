# ipynb-git-hooks
Git hooks to strip output from Jupyter notebooks.

## Why?
These hooks allow to have two mirrored versions of the same repository, one with output in notebooks and one without. The former is useful for presenting results, while the latter's purpose is version control, since it allows to see the code differences between commits.

## Dependencies
* `git`
* `nbstripout`

## How to install the hooks
1. Create the repository for the notebooks with output.
```sh
mkdir $with_output
cd $with_output
git init
```
2. Create the repository for the notebooks without output.
```sh
mkdir $without_output
cd $without_output
git init
```
3. Create a directory for temporary files.
```sh
mkdir $temp
```
4. Do an empty commit in both repository, **with the same commit message**.
```sh
cd $with_output
git commit --allow-empty -m "First commit"
cd $without_output
git commit --allow-empty -m "First commit"
```
5. Remove all files from `.git/hooks` in both repositories (_optional_).
6. Link (or copy) the files `pre-commit` and `post-commit` to `.git/hooks` in both repositories. It's advisable to use absolute paths when creating links.
```sh
ln -s $ipynb-git-hooks/pre-commit $with-output/.git/hooks
ln -s $ipynb-git-hooks/pre-commit $without-output/.git/hooks
ln -s $ipynb-git-hooks/post-commit $with-output/.git/hooks
ln -s $ipynb-git-hooks/post-commit $without-output/.git/hooks
```
7. Link (or copy) the files `process_file-with-output` and `process_file-without-output` respectively to `$with_output/.git/hooks` and `$without_output/.git/hooks`, renaming the link (or the copy) to `process_file`. It's advisable to use absolute paths when creating links.
```sh
ln -s $ipynb-git-hooks/process_file-with-output $with-output/.git/hooks/process_file
ln -s $ipynb-git-hooks/process_file-without-output $without-output/.git/hooks/process_file
```
8. Copy the file `environment` to `.git/hooks` in both repositories, then edit it: `$temp_dir` should be your temporary files directory; `$other_dir` should be `$without_output` in the repository with output, and `$with_output` in the repository without output. It's advisable to use absolute paths for the content of the variables.
```sh
cp $ipynb-git-hooks/environment $with_output/.git/hooks
vi $with_output/.git/hooks      # edit as needed
cp $ipynb-git-hooks/environment $without_output/.git/hooks
vi $without_output/.git/hooks   # edit as needed
```
9. `????`
10. Profit!

## What happens after the hooks are installed?
Every notebook file present in the staged area gets stripped before the commit; the version without output is then committed in the `$without_output` repository, while the version with output is committed in the `$with_output` repository, no matter which one git is invoked in.

Because of this, you can also execute notebooks in the `$without_output` directory, and both version (with and without output) will be put in the correct repository during the commit. Of course, if the staged notebooks are already without output, then the `$with_output` version will also be stripped, so pay attention.

One last remark: if the commit in the `$without_output` repository is aborted with an empty message, the file left in the directory will be the already-stripped one. The non-stripped file can be found in the `$temp` directory.

## What is missing?
These hooks assume that the work is done without changing branches. When I will need different branches, I will try to implement this feature.
