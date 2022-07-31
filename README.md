<img align="right" width="22%" src="https://raw.githubusercontent.com/patbec/zsh-console-icons/master/zsh-console-auto.svg" alt="zsh console icon"/>

# Ansible Role: ZSH

![Ansible Role](https://img.shields.io/ansible/role/d/58811) ![Ansible Quality Score](https://img.shields.io/ansible/quality/58811) ![GitHub](https://img.shields.io/github/license/patbec/ansible-role-zsh)

This is a simple role to install and set up the **zsh-shell** on Linux.

The following steps are supported:
- Install zsh with custom packages
- Set zsh as default shell for the specified users
- Optionally distribute a `.zshrc` file

This role is kept simple, uses the default package manager and contains no overhead. If you have any problems, feel free to create an issue.


## Preparation

Install this role with Ansible Galaxy.
```
ansible-galaxy install patbec.zsh
```

## Variables

Default variables in this playbook.

| Name                 | Description                                          | Default Value        |
| -------------------- | ---------------------------------------------------- | -------------------- |
| zsh_use_config       | Distribute a custom `.zshrc` for each user.          | `false`              |
| zsh_config_template  | Jinja template for the `.zshrc` file.                | `zshrc.j2`           |
| zsh_config_dest      | Path of the `.zshrc` file.                           | `$HOME/.zshrc`       |
| zsh_config_backup    | Creates a backup before overwriting.                 | `true`               |
| zsh_config_overwrite | Overwrites the `.zshrc` file if it exists.           | `false`              |
| zsh_users            | A list of users who should get zsh as default shell. | `{{ ansible_user }}` |
| zsh_dependencies     | A listing of additional packages to be installed.    | `[]`                 |

## Sample playbooks

Here are a few examples of how this role can be used.

  - [Sample 1: Basic](https://github.com/patbec/ansible-role-zsh#sample-1-basic)<br>
  Install zsh only for the default ssh connection user.<br><br>
  - [Sample 2: Multiple users](https://github.com/patbec/ansible-role-zsh#sample-2-multiple-users)<br>
  Install zsh for a list of specified users.<br><br>
  - [Sample 3: User defined config file](https://github.com/patbec/ansible-role-zsh#sample-3-user-defined-config-file)<br>
  Install zsh for a list of specified users with a custom `.zshrc` file.<br><br>
  - [Sample 4: Custom dependencies](https://github.com/patbec/ansible-role-zsh#sample-4-custom-dependencies)<br>
  Install zsh for a list of specified users with a custom `.zshrc` file and a custom dependency.<br><br>
  - [Sample 5: Autosuggestions and Syntax-Highlighting](https://github.com/patbec/ansible-role-zsh#sample-5-autosuggestions-and-syntax-highlighting)<br>
  Complete example of a ZSH shell with autocompletion and syntax highlighting. <sup>[Sample Preview](https://github.com/patbec/ansible-role-zsh#sample-preview)</sup><br><br>

### Sample 1: Basic
Install zsh only for the default ssh connection user.
- Install zsh for the current user.
- Set it as default shell.

```
- name: zsh
  hosts: all
  vars:
  roles:
    - patbec.zsh
  tasks:
```
### Sample 2: Multiple users
Install zsh for a list of specified users.
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
### Sample 3: User defined config file
Install zsh for a list of specified users with a custom `.zshrc` file.
- Install zsh for the specified users.
- Set it as default shell.
- Distribute a custom `.zshrc` for each user.

```
- name: zsh
  hosts: all
  vars:
    zsh_use_config: true
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

### Sample 4: Custom dependencies
Install zsh for a list of specified users with a custom `.zshrc` file and a custom dependency.
- Install zsh for the specified users.
- Set it as default shell.
- Distribute a custom `.zshrc` for each user.
- Install custom dependencies.

```
- name: zsh
  hosts: all
  vars:
    zsh_use_config: true
    zsh_config_overwrite: true

    zsh_users:
      - lorem
      - ipsum

    zsh_dependencies:
      - exa
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
alias ll='exa --group-directories-first --all --long --binary --group --classify --grid'
alias la='exa --group-directories-first --all --long --binary --group --header --links --inode --modified --blocks --time-style=long-iso --color-scale'
alias lx='exa --group-directories-first --all --long --binary --group --header --links --inode --modified --blocks --time-style=long-iso --color-scale --extended'
```

### Sample 5: Autosuggestions and Syntax-Highlighting
Complete example of a ZSH shell with [autosuggestions](https://github.com/zsh-users/zsh-autosuggestions#zsh-autosuggestions), [syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting#zsh-syntax-highlighting-) and [exa](https://the.exa.website) as `ls` alternative.
- Install zsh for the specified user.
- Set it as default shell.
- Distribute a custom `.zshrc` for this user.
- Install custom dependencies.

```
- name: zsh
  hosts: all
  vars:
    zsh_use_config: true
    zsh_config_overwrite: true

    zsh_users:
      - lorem
      - ipsum

    zsh_dependencies:
      - exa
      - zsh-autosuggestions
      - zsh-syntax-highlighting
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

source  /usr/share/zsh-autosuggestions/zsh-autosuggestions.zsh
source  /usr/share/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh

alias ls='exa --group-directories-first'
alias ll='exa --group-directories-first --all --long --binary --group --classify --grid'
alias la='exa --group-directories-first --all --long --binary --group --header --links --inode --modified --blocks --time-style=long-iso --color-scale'
alias lx='exa --group-directories-first --all --long --binary --group --header --links --inode --modified --blocks --time-style=long-iso --color-scale --extended'

HISTFILE="$HOME/.zsh_history"
HISTSIZE=50000
SAVEHIST=50000
setopt INC_APPEND_HISTORY

{% if zsh_additional_lines is defined %}
#
# Host specific additions
#
{{ zsh_additional_lines }}
{% endif %}
```

#### Sample Preview

Preview of the configuration from [step 5](https://github.com/patbec/ansible-role-zsh#sample-5-autosuggestions-and-syntax-highlighting), using [autosuggestions](https://github.com/zsh-users/zsh-autosuggestions#zsh-autosuggestions), [syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting#zsh-syntax-highlighting-) and [exa](https://the.exa.website) as `ls` alternative. You can find the used [ANSI console colors here](https://github.com/patbec/zsh-console-icons#colors).
<img width="100%" height="450px" alt="zsh console" src="https://raw.githubusercontent.com/patbec/zsh-console-icons/master/zsh-console.svg">

## Licence

This project is licensed under MIT - See the [LICENSE](https://github.com/patbec/ansible-role-zsh/blob/master/LICENSE) file for more information.

---

&uarr; [Back to top](https://github.com/patbec/ansible-role-zsh#ansible-role-zsh)