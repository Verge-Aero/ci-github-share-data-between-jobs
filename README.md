# ci-github-share-data-between-jobs
Allows Github actions to share data using Github action caches between Jobs.

Since jobs can run in different environments, this can be very useful.

# Inputs
There's a `path_list` and a `name_list` which are related to each other.

The inputs are strings which contain a list of paths to the files you want to save. The list is encoded using space delimiters.
There's an input name_list which should be renamed as a "key_list". These keys dictate the key under which the corresponding file will be stored.

# Usage
Let's say you have Job A that creates a file you want to access in Job B.

In Job A, use this step:
```
       - name: Save my favorite files
         uses: Verge-Aero/ci-github-share-data-between-jobs@main
         with:
           path_list: 'README.md ./a/path/to/another/file.txt'
           name_list: 'myReadme otherSpecialFile'
```
The first item of the `path_list` corresponds with the first item of the `name_list`, and so on.

The above step, for example, will save the README.md file under the key `myReadme` which can later be loaded (as shown below).

Then from Job B, you can add the following step. 

> Remember you must mark Job A as a `needs` requirement in Job B, to ensure Job A completes before Job B starts.

```
       - name: Load data
         uses: Verge-Aero/ci-github-share-data-between-jobs@main
         with:
           path_list: 'README.md ./a/path/to/another/file.txt'
           name_list: 'myReadme otherSpecialFile'
           load: true
```

The result is that after this step runs, Job B will have a copy of that same file.

# Pitfalls
* Since this takes advantage of Github Action caches, you can't overwrite an existing key.
* If you want to share a directory, which can be written in various ways (with or without trailing backslash, for example) it's important to ensure you stick to a convention.
