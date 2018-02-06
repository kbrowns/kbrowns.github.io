# SSH Cheats

## ~/.ssh/config

```
host stash.csgsystems.com
  HostName stash.csgsystems.com
  IdentityFile ~/.ssh/brok02_rsa
  User brok02
host github.com
  HostName github.com
  IdentityFile ~/.ssh/id_rsa
host git-codecommit.*.amazonaws.com
  User APKAI3O4FF6LPUXZWYBA
  IdentityFile ~/.ssh/brok02
```

## Key Gen

```
ssh-keygen -t rsa
```

## Extract Public

```
ssh-keygen -y -e -f ~/.ssh/brok02_rsa
```
