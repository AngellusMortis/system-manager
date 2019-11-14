# Yet Another Ansible System Manager

As developers we have many tools to build reproducible envionrments: Packer,
Vagrant, Docker, Ansible, Chef, Puppet, etc. The one thing we do not often have
made in a reproducible way is our local developer machines. Why not use one of
those great tools above (Ansible) to do this for us?

This project is just designed to be a collection of Ansible roles and/or playbooks
to bootstrap and manage a developer machine.

## Requirements

The only thing you need to get started using this is a supported OS and
Git+Ansible.

If you re not explicitly using a supported OS, but you are using a favor based
on that OS (Manjaro instead of Arch or Mint instead of Ubuntu), everything may
still work. It is certainly worth a try. If it does not, PRs are always
welcome to add support for your preferred distro as long as you do not break
any of the existing ones.

### Arch Requirements

```bash
$ sudo pacman -S ansible git
```

### Ubuntu Requirements

Tested against Ubuntu 19.04

```bash
$ sudo apt install ansible git
```

## Setup

At the bare minmal, you need initalize submodules and copy the
`user.yml.example` to `user.yml` and tweak it to your liking.

```bash
$ cd system-manager
$ git submodule update --init
$ cd system-manager/group_vars/all
$ cp user.yml.example user.yml
$ # EDIT user.yml to your liking
```

### User Configs

`system-manager` provides a folder, `files/user` that is completely git ignored. You
can put anything in here that you need to reference elsewhere in your `user.yml`.

For example, what this is what I do:

```bash
$ cd system-manager/files/user
$ git clone git@github.com:AngellusMortis/mortis-configs.git
$ cd system-manager/group_vars/all
$ ln -s ../../files/user/mortis-configs/vars/user.arch.work.yml user.yml
```

What way I can version all of my things, including custom themes!

## Usage

To run the full `system-manager`, it is pretty straightforward:

```bash
$ cd system-manager
$ ansible-playbook main.yml
```

`system-manager` also provides a bunch of tags if you would like to only run a
subset of the functionality it provides.

| Tag | Description |
|-----|-------------|
| `core` | Core set of sytem management actions. Primarily runs system updates |
| `helpers` | Subset of `core`. Copies over some helper scripts made to automate common tasks. |
| `user` | A collection of tasks to configure various local user settings, like your `bashrc` or `gitconfig`. |
| `cloud` | Subset of `user`. Intalls and configures a Cloud sync provider like OneDrive. |
| `boot` | Tasks to theme your boot experience. Include Grub and Plymouth theming. |
| `desktop` | Tasks to configure your desktop environment. |
| `graphics` | Subset of `desktop`. Installs graphics drivers. |
| `extra` | Tasks to copy artbiary files and install extra packages. |
| `dev` | Tasks to bootstrap dev environments. Installs tools like Docker, NPM/Node.js, an Kubernetes. Also can clone artbiary git repos. |

You can run a specific tag or tags with the following:

```bash
$ cd system-manager
$ ansible-playbook main.yml --tags core
$ ansible-playbook main.yml --tags boot,desktop
```

## Changing Display Managers

When you want to change your default display manager, it takes a bit of extra
effort to switch them.

### Arch Display Manager

```bash
$ sudo systemctl stop display-manager
$ sudo systemctl disable display-manager
```

Update `desktop_display_manager` in your `user.yml` and then run the
`desktop` tag and your new display manager will be enabled.

### Ubuntu Display Manager

Update `desktop_display_manager` in your `user.yml` and then run the
`desktop` tag. Then run the following command to choose your new default
display manager:

```bash
sudo dpkg-reconfigure name_of_display_manager
```
