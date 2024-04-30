=======================================
Ansible roles for IEEE 802.11p on Linux
=======================================

For general documentation, see the `superordinate README file
<../README.rst>`__,

For role-specific documentation, see the corresponding
``roles/<role>/README.rst``.

tags
====

The roles support the following tags:

:online:
  This tag can be used to skip any task which requires Internet access.

:configure:
  This tag can be used to load kernel modules and configure interfaces
  onlys. Building and installation stages will be skipped. Useful, e.g.,
  after reboot.
