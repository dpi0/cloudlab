# push-to-repo

**make sure you are in the docker group, and have logged out/restarted after installing docker**

this service+timer will run $HOME/push-to-repo.sh script every 2 days Asia/Kolkata time at 0200.

```bash
mkdir -p ~/.config/systemd/user

ln -s ~/cloudlab/systemd/push-to-repo/push-to-repo.service ~/.config/systemd/user
ln -s ~/cloudlab/systemd/push-to-repo/push-to-repo.timer ~/.config/systemd/user

systemctl --user daemon-reload
systemctl --user enable --now push-to-repo.service
systemctl --user enable --now push-to-repo.timer

systemctl --user status push-to-repo.service
systemctl --user status push-to-repo.timer

journalctl --user -u push-to-repo.service --since "5 minutes ago"
```
