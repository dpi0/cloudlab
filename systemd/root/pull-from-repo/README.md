# `pull-from-repo`

**make sure you are in the docker group, and have logged out/restarted after installing docker**

on a headless VPS, the general best practice is to use system-level (global) systemd services and timers

the problem with --user level serives is that: systemd --user instances only run when the user is logged in (unless loginctl enable-linger is used).

VPS systems typically run headless without persistent user sessions.

and global systemd units run reliably at boot and on schedule, regardless of login state.

so will be using global (root) level systemd units for my homelab and cloudlab.

this service+timer will run $HOME/scripts/pull-from-repo.sh.sh script every 30 minutes.

**to remove all --user systemd**

```bash
systemctl --user daemon-reload

systemctl --user list-units --type=service 
systemctl --user list-units --type=timer

# stop them individually
systemctl --user disable --now UNIT.service
systemctl --user disable --now UNIT.timer
# there is no way to bulk stop/remove them

rm ~/.config/systemd/user/*.service
rm ~/.config/systemd/user/*.timer
# or simply trash ~/.config/systemd/user
```

```bash
sudo mkdir -p /etc/systemd/system

# use cp for production envs for stability
# use ln -s only when live-testing or in a dev env
sudo cp ~/cloudlab/systemd/root/pull-from-repo/pull-from-repo.service /etc/systemd/system/
sudo cp ~/cloudlab/systemd/root/pull-from-repo/pull-from-repo.timer /etc/systemd/system/

sudo chmod 644 /etc/systemd/system/pull-from-repo.{service,timer}

sudo systemctl daemon-reload
# daemon-reexec restarts the systemd-binary as well. 
# Higher impact than daemon-reload. Use cautiously.
# should be reserved for lower-level recovery or post systemd upgrade steps. or in rare edge cases where systemd is misbehaving or its in-memory state is inconsistent.

sudo systemctl list-unit-files --type=timer | grep --color -i -n 'pull' -A 5 -B 5
sudo systemctl list-unit-files --type=service | grep --color -i -n 'pull' -A 5 -B 5

sudo systemctl enable --now pull-from-repo.timer
# why do we only enable the timer?
# because enabling the .service would schedule it to start at boot, not on a timer.
# only enable the .service directly if
# You want it to run at boot time unconditionally

sudo systemctl status pull-from-repo.timer

journalctl -u pull-from-repo.service --since "5 minutes ago"
```
