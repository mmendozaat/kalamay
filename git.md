### Change email address in git history
[https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History)

```
$ git filter-branch --commit-filter '
        if [ "$GIT_AUTHOR_EMAIL" = "old@email.com" ];
        then
                GIT_AUTHOR_NAME="Author Name";
                GIT_AUTHOR_EMAIL="new@email.com";
                git commit-tree "$@";
        else
                git commit-tree "$@";
        fi' HEAD
```

### Reset local and remote origin head to commit
[https://stackoverflow.com/questions/17667023/git-how-to-reset-origin-master-to-a-commit](https://stackoverflow.com/questions/17667023/git-how-to-reset-origin-master-to-a-commit)

```
git reset --hard <commit>
git push -f origin master
```

### Reset remote origin head to commit

```
git push --force origin e3f1e37:master
```
