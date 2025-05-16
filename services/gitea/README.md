# `gitea server`

- open up `SSH_PORT` in VPS firewall
- set admin username, password and email
- best to setup oauth first for [Codeberg](https://forgejo.org/docs/latest/user/oauth2-provider/#using-codeberg-as-an-authentication-source) and GitHub 
- add the ssh keys, create `ssh-keygen -t ed25519 -f $HOME/.ssh/gitea -C "Gitea Cloudlab"` paste in the created `gitea.pub` content 
- in your host's ``~/.ssh/config`` add:
  ```bash
  Host git-ssh.bob.tech
    HostName git-ssh.bob.tech
    User git
    IdentityFile ~/.ssh/gitea
    IdentitiesOnly yes
  ```
- and make sure `ssh-agent` is running on your system `echo $SSH_AUTH_SOCK`


# `gitea runner`

- first start the `gitea-server` 
- <https://git.bob.tech/user/settings/actions/runners> **Create a New Runner**
- copy the token and add to `GITEA_RUNNER_REGISTRATION_TOKEN`
- generate default config `docker run --entrypoint="" --rm -it docker.io/gitea/act_runner:latest act_runner generate-config > config.yml`
- and place in appropriate volume mount for `/data/config.yml` for the runner
- start `gitea-runner` and restart `gitea-server`

to install themes taken from <https://git.sainnhe.dev/sainnhe/gitea-themes>

```bash
docker exec -u git GITEA_SERVICE_NAME sh -c '
mkdir -p /data/gitea/public/assets/css && \
cd /tmp && \
git clone --depth 1 --branch master https://git.sainnhe.dev/sainnhe/gitea-themes.git && \
cp gitea-themes/dist/*.css /data/gitea/public/assets/css/ && \
rm -rf gitea-themes'

# restart gitea-server
```
