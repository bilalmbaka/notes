# Branches

### show all remote branches

```bash
git branch -r
```

### sync all remote branches

```bash
git fetch <origin>
```

### checkout to a pr
```bash
git fetch origin pull/$ID/head:$BRANCHNAME
```

where $ID is the pull request id and $BRANCHNAME is the name of the new branch that you want to create. Once you have created the branch, then simply

git checkout $BRANCHNAME
For instance, let's imagine you want to checkout pull request #2 from the origin main branch:

```bash
git fetch origin pull/2/head:MASTER
```

```bash
git pull origin pull/939/head
```

### remove all origins that have been deleted

```bash
git remote prune <remote_name>
```