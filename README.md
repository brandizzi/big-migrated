This repository, together with [brandizzi/big](https://github.com/brandizzi/big), tests how [Git LFS](https://git-lfs.github.com/) works.

In [brandizzi/big](https://github.com/brandizzi/big), we:
1. committed some large files to a repository;
2. removed the large file;
3. committed another large file;
4. enabled Git LFS in that repository;
5. committed yet another large file.

The result a repository with big, heavy Git objects in its history:

    $  git rev-list --objects --all |\
        git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' |\
        sed -n 's/^blob //p' |\
        sort --numeric-sort --key=2 |\
        cut -c 1-12,41- |\
        $(command -v gnumfmt || echo numfmt) --field=2 --to=iec-i --suffix=B --padding=7 --round=nearest
    78981922613b      2B file.txt
    8baef1b4abc4      4B file.txt
    5b6464ab537e      9B numbers.txt
    b634d85f0161     42B .gitattributes
    5fe304ad9c48    133B right.pdf
    0ecdbebfa077   19MiB wrong.pdf
    8a414f62add7   19MiB How_To_Embrace_Remote_Work_Trello_Ultimate_Guide.pdf

We then created a new repository (this one, brandizzi/big-migrated) by calling

    git lfs migrate import --everything

This repository does not have those big files among the Git objects:

    $  git rev-list --objects --all |\
             git cat-file --batch-check='%(objecttype) %(objectname) %(objectsize) %(rest)' |\
             sed -n 's/^blob //p' |\
             sort --numeric-sort --key=2 |\
             cut -c 1-12,41- |\
             $(command -v gnumfmt || echo numfmt) --field=2 --to=iec-i --suffix=B --padding=7 --round=nearest
    8d16c62e5f88     84B .gitattributes
    d010a34d6795     84B .gitattributes
    06388162f42c    126B file.txt
    931bec11f6a5    126B numbers.txt
    9c2119e50973    126B file.txt
    5fe304ad9c48    133B right.pdf
    6c20913dea7f    133B How_To_Embrace_Remote_Work_Trello_Ultimate_Guide.pdf
