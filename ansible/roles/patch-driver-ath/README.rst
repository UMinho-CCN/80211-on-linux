This Ansible role prepares the kernel to make it aware of IEEE 802.11p.

Specifically, the wireless device driver ``ath9k`` is patched, compiled
and loaded.

See `vars/main.yml <vars/main.yml>`__ to see what can be configured.

This needs to be re-run after reboots.


