<img align="right" width="15%" src="https://user-images.githubusercontent.com/29308797/163578065-cf057ee2-2dd5-4f19-8bf1-9ab71962b98b.png" alt="zsh console"/>

# Ansible Role ZSH

This is a basic ansible role to set up **ZSH** on Ubuntu or macOS. It should also work on many other Linux variants.

> If you have any problems, feel free to create an issue.


## Preparation

Install this role with Ansible Galaxy.
```
ansible-galaxy install patbec.zsh
```

## Variables

Default variables in this playbook.

```
# Dependencies that are to be installed. The default package manager on the respective system is used.
zsh_dependencies: []

# Set this variable in the playbook to distribute a custom .zshrc for each user.
zsh_use_config: false

# The template used to create the user's .zshrc file when zsh_use_config is true.
zsh_config_template: "zshrc.j2"

# Destination path for the .zshrc config file.
zsh_config_dest: "$HOME/.zshrc"

# Create a backup from the existing .zshrc files when the role changes it.
zsh_config_backup: true

# Overwrites the .zshrc if the content differs with the existing file.
zsh_config_overwrite: false

# Listing of users for whose ZSH is to be set up and used as the default shell.
zsh_users:
  - "{{ ansible_user }}"
```

## Sample playbooks

Here are a few examples of how this role can be used.

### Sample 1

- Install zsh for the current user.
- Set it as default shell.

> Note: The default value of the `zsh_users` variable is `ansible_user`.

```
- name: zsh
  hosts: all
  vars:
  roles:
    - patbec.zsh
  tasks:
```
### Sample 2

- Install zsh for the specified users.
- Set it as default shell.

```
- name: zsh
  hosts: all
  vars:
    zsh_users:
      - lorem
      - ipsum
  roles:
    - patbec.zsh
  tasks:
```
### Sample 3

- Install zsh for the specified users.
- Set it as default shell.
- Distribute a custom `.zshrc` for each user.

```
- name: zsh
  hosts: all
  vars:
    zsh_use_config: true

    zsh_config_template: "zshrc.j2"
    zsh_config_dest: "$HOME/.zshrc"
    zsh_config_backup: true
    zsh_config_overwrite: true

    zsh_users:
      - lorem
      - ipsum
  roles:
    - patbec.zsh
  tasks:
```

`zshrc.j2` file in your templates folder:
```
#
# {{ ansible_managed }}
#

{% if zsh_config_overwrite is true %}
# Changes to this file will be overwritten by Ansible.
{% endif %}

# Add your custom content here.
```

### Sample 4
- Install zsh for the specified users.
- Set it as default shell.
- Distribute a custom `.zshrc` for each user.
- Install custom dependencies.

```
- name: zsh
  hosts: all
  vars:
    zsh_dependencies:
      - exa

    zsh_use_config: true

    zsh_config_template: "zshrc.j2"
    zsh_config_dest: "$HOME/.zshrc"
    zsh_config_backup: true
    zsh_config_overwrite: true

    zsh_users:
      - lorem
      - ipsum
  roles:
    - patbec.zsh
  tasks:
```
`zshrc.j2` file in your templates folder:
```
#
# {{ ansible_managed }}
#

{% if zsh_config_overwrite is true %}
# Changes to this file will be overwritten by Ansible.
{% endif %}

autoload colors && colors

PROMPT="%(?:%{$fg_bold[green]%}➜ :%{$fg_bold[red]%}➜ )"
PROMPT+=" %{$fg[cyan]%}%c%{$reset_color%} "

alias ls='exa --group-directories-first'
alias  l='exa --group-directories-first --all --long --binary --group --classify'
alias ll='exa --group-directories-first --all --long --binary --group --classify --grid'
alias la='exa --group-directories-first --all --long --binary --group --header --links --inode --created --modified --accessed --blocks --time-style=long-iso --color-scale'
alias lx='exa --group-directories-first --all --long --binary --group --header --links --inode --created --modified --accessed --blocks --time-style=long-iso --color-scale --extended'
```

### Sample 5

> This sample playbook supports **Ubuntu** and **macOS** as target systems.

- Install zsh for the specified user.
- Set it as default shell.
- Distribute a custom `.zshrc` for this user.
- Install custom dependencies.

`/playbook-test.yml` file in your playbook folder:
```
- name: zsh
  hosts: all
  vars:
    zsh_dependencies:
      - exa
      - zsh-autosuggestions
      - zsh-syntax-highlighting

    zsh_use_config: true

    zsh_config_template: "zshrc.j2"
    zsh_config_dest: "$HOME/.zshrc"
    zsh_config_backup: true
    zsh_config_overwrite: true

  roles:
    - patbec.zsh
  tasks:
```

`/inventory.txt` file in your playbook folder:
```
[servers]
thinkbox ansible_user=patbec ansible_host=192.168.178.100

[private]
macos ansible_user=patbec ansible_connection=local
```

`/host_vars/thinkbox.yml` file in your host specific folder:
```
architecture_independent_data: /usr/share

zsh_users:
  - patbec
  - root

zsh_additional_lines: |
  alias config='cd /etc/nginx/sites-enabled/ && ls'
```

`/host_vars/macos.yml` file in your host specific folder:
```
architecture_independent_data: /usr/local/share

zsh_users:
  - patbec

zsh_additional_lines: |
  alias p='cd /Users/patbec/Documents/GitHub'
```

`/templates/zshrc.j2` file in your templates folder:
```
#
# {{ ansible_managed }}
#

{% if zsh_config_overwrite is true %}
# Changes to this file will be overwritten by Ansible.
{% endif %}

autoload colors && colors

PROMPT="%(?:%{$fg_bold[green]%}➜ :%{$fg_bold[red]%}➜ )"
PROMPT+=" %{$fg[cyan]%}%c%{$reset_color%} "

source {{ architecture_independent_data }}/zsh-autosuggestions/zsh-autosuggestions.zsh
source {{ architecture_independent_data }}/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh

alias ls='exa --group-directories-first'
alias  l='exa --group-directories-first --all --long --binary --group --classify'
alias ll='exa --group-directories-first --all --long --binary --group --classify --grid'
alias la='exa --group-directories-first --all --long --binary --group --header --links --inode --created --modified --accessed --blocks --time-style=long-iso --color-scale'
alias lx='exa --group-directories-first --all --long --binary --group --header --links --inode --created --modified --accessed --blocks --time-style=long-iso --color-scale --extended'

{% if zsh_additional_lines is defined %}
#
# Host specific additions
#
{{ zsh_additional_lines }}
{% endif %}
```

The look of the shell after these changes:
<img width="100%" alt="zsh-shell-preview" src="https://user-images.githubusercontent.com/29308797/163578137-67bb9dbf-5fe7-4109-97e5-b9c22a5afd2b.png">

### Sample 6

- Install zsh for the specified users.
- Set it as default shell.
- Distribute a custom `.zshrc` for each user.
- Install custom dependencies.
```
- name: zsh
  hosts: all
  vars:
    zsh_dependencies:
      - exa
      - zsh-autosuggestions
      - zsh-syntax-highlighting

    zsh_use_config: true

    zsh_config_template: "zshrc.j2"
    zsh_config_dest: "$HOME/.zshrc"
    zsh_config_backup: true
    zsh_config_overwrite: true

    zsh_users:
      - lorem
      - ipsum
  roles:
    - patbec.zsh
  tasks:
```
`zshrc.j2` file in your templates folder:
```
#
# {{ ansible_managed }}
#

{% if zsh_config_overwrite is true %}
# Changes to this file will be overwritten by Ansible.
{% endif %}

autoload colors && colors

PROMPT="%(?:%{$fg_bold[green]%}➜ :%{$fg_bold[red]%}➜ )"
PROMPT+=" %{$fg[cyan]%}%c%{$reset_color%} "

source /usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh
source /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh

alias ls='exa --group-directories-first'
alias  l='exa --group-directories-first --all --long --binary --group --classify'
alias ll='exa --group-directories-first --all --long --binary --group --classify --grid'
alias la='exa --group-directories-first --all --long --binary --group --header --links --inode --created --modified --accessed --blocks --time-style=long-iso --color-scale'
alias lx='exa --group-directories-first --all --long --binary --group --header --links --inode --created --modified --accessed --blocks --time-style=long-iso --color-scale --extended'
```
