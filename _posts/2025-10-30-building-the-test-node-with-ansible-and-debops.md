---
title: Building the test node with Ansible and Debops
date: 2025-10-30 12:37:26.000000000 +00:00
categories: [Packet Radio]
tags: [ansible, debops, packet-radio, snopac]
---
# Ansible and Debops

I spent some of my early professional years doing what is now known as
'[DevOps](https://en.wikipedia.org/wiki/DevOps)'. As well as developing
software for data communications products like
[X.25](https://en.wikipedia.org/wiki/X.25) Packet Switched Nodes, I also
developed test harnesses, build tools etc.
Thus, I have always been drawn to tools that ease the process of
building, configuring and managing software devices.

I came across
[Ansible](https://en.wikipedia.org/wiki/Ansible_(software)) some years
ago and adopted it for my personal needs (I stopped developing software
professionally about 25 years ago, but still 'tinker').
Therefore it was natural that I would seek to leverage Ansible when building a packet node.

More recently, I came across [Debops](https://debops.org), which is a
suite of ansible playbooks focused on the management of secure
[Debian](https://en.wikipedia.org/wiki/Debian)-based
[Linux](https://en.wikipedia.org/wiki/Linux) environments.
This includes the [Raspberry Pi](https://www.raspberrypi.com) that I
intend to use as the hardware platform for the nodes.

The reason for this sidebar is that I have chosen to create Ansible
[Roles](https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_reuse_roles.html#roles)
for each of the software components and distribute them as an Ansible
[Collection](https://docs.ansible.com/ansible/latest/collections_guide/index.html#collections).\
By using Ansible to manage the node, I can 'cookie-cutter' additional
nodes and manage their configuration centrally.

NB: The AWX Project is a trademark of Red Hat, Inc., used with permission

