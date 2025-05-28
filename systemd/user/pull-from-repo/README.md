# `pull-from-repo`

**make sure you are in the docker group, and have logged out/restarted after installing docker**

this service+timer will run $HOME/scripts/pull-from-repo.sh.sh script every 30 minutes.

```bash
mkdir -p ~/.config/systemd/user

ln -s ~/cloudlab/systemd/user/pull-from-repo/pull-from-repo.service ~/.config/systemd/user
ln -s ~/cloudlab/systemd/user/pull-from-repo/pull-from-repo.timer ~/.config/systemd/user

systemctl --user daemon-reload
systemctl --user enable --now pull-from-repo.timer

systemctl --user status pull-from-repo.service
systemctl --user status pull-from-repo.timer

journalctl --user -u pull-from-repo.service --since "5 minutes ago"
```
