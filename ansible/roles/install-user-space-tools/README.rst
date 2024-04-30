This Ansible role prepares user space tools to make them aware of IEEE 802.11p.

Specifically, there are four components that need to be taken care of:

* ``iw`` command: need to be recent enough to be aware of OCB mode,
  so we can put interfaces in OCB mode later on
* wireless regulatory database and central regulatory domain agent
  (CRDA): must allow the 802.11p band in our regulatory domain
* central regulatory domain agent (CRDA): needs to be made aware of
  OCB mode (so it can read our modified version of the regulatory
  database).

See `vars/main.yml <vars/main.yml>`__ to see what can be configured.

This needs to be re-run after reboots.
