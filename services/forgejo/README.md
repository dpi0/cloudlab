# `forgejo`


## to make SSH work

- preset the `FORGEJO__server__SSH_PORT` and `FORGEJO__server__SSH_DOMAIN`
- open up the set PORT in VPS' firewall
- `FORGEJO__server__SSH_DOMAIN` has to be set to a new domain like `git-ssh.bob.tech` which points to VPS' PUBLIC IP (turn off proxy in cloudflare DNS record)
- haven't found a better than this, as traefik doesn't allow ssh by default. there are solutions for forgejo+traefik+ssh but haven't tried them out (found them too complex)
- after the setup for forgejo, 
  ```bash
  ssh-keygen -t ed25519 -f $HOME/.ssh/forgejo-cloudlab -C "Forgejo SSH Cloudlab"
  cat $HOME/.ssh/forgejo-homelab.pub | wl-copy
  ```
  add this key to <https://git.bob.tech/user/settings/keys>
- test the ssh connection: `ssh -T -p SSH_PORT -i $HOME/.ssh/forgejo-cloudlab git@git-ssh.bob.tech` should say something like `Hi there, USERNAME!`

## to push a new repository via SSH

**first startup your ssh-agent (check your OS docs) and add the ssh-key to ssh/config**

```bash
# make sure agent is running
ps aux | grep ssh-agent
echo $SSH_AUTH_SOCK

# this permanently add your particular ssh key to your agent
cat ~/.ssh/config <<EOF
Host git-ssh.bob.tech
  HostName git-ssh.bob.tech
  User git
  IdentityFile ~/.ssh/forgejo-cloudlab
  IdentitiesOnly yes
EOF

# to make ssh work temporarily for current session only
eval "$(ssh-agent -s)"; ssh-add $HOME/.ssh/forgejo-cloudlab
```

**then, for your local repo**

```bash
git remote add forgejo "[git@git.bob.tech:SSH_PORT]:FORGEJO_USERNAME/CURRENT_REPO_NAME.git"

# verify
git remote -v

# make sure to have FORGEJO__repository__ENABLE_PUSH_CREATE_USER=true in compose env
# refer https://forgejo.org/docs/latest/admin/config-cheat-sheet/
git push forgejo BRANCH_NAME --force
```

## to push via HTTPS (not recommended)

```bash
# add new remote "forgejo"
git remote add forgejo "https://git.homelab.bob.tech/FORGEJO_USERNAME/CURRENT_REPO_NAME.git"

# verify remote
git remote -v

# make sure to have FORGEJO__repository__ENABLE_PUSH_CREATE_USER=true in compose env
git push forgejo BRANCH_NAME --force
# enter FORGEJO_USERNAME
# enter FORGEJO_PASSWORD

