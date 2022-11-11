<img src="https://raw.githubusercontent.com/roycastro/mac-dev-setup/master/files/Mac-Dev-Playbook-Logo.png" width="250" height="156" alt="Mac Dev Playbook Logo" />

# Mac Development Ansible Playbook

This playbook installs and configures most of the software I use on my Mac for web development. Please take into account that some features are not complete an consequently this is a WIP. This work is heavily based on [geerlinguy](https://github.com/geerlingguy)'s awesome tutorials and I recommend you visit this [repository](https://github.com/geerlingguy/mac-dev-playbook)

```text
 Note: All the testing has been carried out in [macOS Ventura](https://www.apple.com/macos/ventura/)
```

## Installation

1. Ensure Apple's command line tools are installed (`xcode-select --install` to launch the installer).
2. [Install Oh My Zsh](https://ohmyz.sh/)
3. [Install Ansible](http://docs.ansible.com/intro_installation.html).
4. Install SDKMan utility as it will be needed to install multiple Java versions simultaneously. [SDKMan](https://sdkman.io/install)

```bash
curl -s "https://get.sdkman.io" | zsh
```

5. Clone this repository to your local drive.
6. Go to the repository and run the following commands to use Ansible in a Python virtual environment:

   ```bash
     python -m virtualenv ansible
     source ansible/bin/activate
     pip install ansible
   ```

7. Run `ansible-galaxy install -r requirements.yml` inside this directory to install required Ansible roles.
8. Run `ansible-playbook main.yml -i inventory --ask-become-pass` inside this directory. Enter your account password when prompted. IMPORTANT: This playbook will install and configure a lot of software, so it may take a while to run.

```text
 Note: If some Homebrew commands fail, you might need to agree to Xcode's license or fix some other Brew issue. Run `brew doctor` to see if this is the case.
```

### Use with a remote Mac

You can use this playbook to manage other Macs as well; the playbook doesn't even need to be run from a Mac at all! If you want to manage a remote Mac, either another Mac on your network, or a hosted Mac like the ones from [MacStadium](https://www.macstadium.com), you just need to make sure you can connect to it with SSH:

1. (On the Mac you want to connect to:) Go to System Preferences > Sharing.
2. Enable 'Remote Login'.

```text
 You can also enable remote login on the command line:

     sudo systemsetup -setremotelogin on
```

Then edit the `inventory` file in this repository and change the line that starts with `127.0.0.1` to:

```text
[ip address or hostname of mac]  ansible_user=[mac ssh username]
```

If you need to supply an SSH password (if you don't use SSH keys), make sure to pass the `--ask-pass` parameter to the `ansible-playbook` command.

### Running a specific set of tagged tasks

You can filter which part of the provisioning process to run by specifying a set of tags using `ansible-playbook`'s `--tags` flag. The tags available are `dotfiles`, `homebrew`, `mas`, `extra-packages` and `osx`.

```bash
    ansible-playbook main.yml -i inventory -K --tags "dotfiles,homebrew"
```

## Overriding Defaults

You can override any of the defaults configured in `default.config.yml` by creating a `config.yml` file and setting the overrides in that file. For example, you can customize the installed packages and apps with something like:

```yaml
homebrew_installed_packages:
  - cowsay
  - git
  - go

mas_installed_apps:
  - { id: 443987910, name: "1Password" }
  - { id: 498486288, name: "Quick Resizer" }
  - { id: 557168941, name: "Tweetbot" }
  - { id: 497799835, name: "Xcode" }

composer_packages:
  - name: hirak/prestissimo
  - name: drush/drush
    version: "^8.1"

gem_packages:
  - name: bundler
    state: latest

npm_packages:
  - name: webpack

pip_packages:
  - name: mkdocs
```

Any variable can be overridden in `config.yml`; see the supporting roles' documentation for a complete list of available variables.

## Included Configuration (Default)

Also as part of the playbook my [dotfiles](https://github.com/roycastro/mac-dev-dotfiles) are also installed into the current user's home directory, including the `.osx` dotfile for configuring many aspects of macOS for better performance and ease of use ( hopefully ). You can disable dotfiles management by setting `configure_dotfiles: no` in your configuration.

### Configurations needed after the execution of the playbook

- After installing fzf using the playbook you have to enable autocompletion and key bindings as it's assumed you will use it in the .zshrc config. You can execute the following commands to do so (TODO: automate this process):

```bash
          $(brew --prefix)/opt/fzf/install
```

- There is a vim configuration in the repo, but it's still pending the actual installation:

```bash
        mkdir -p ~/.vim/autoload
        mkdir -p ~/.vim/bundle
        cd ~/.vim/autoload
        curl https://raw.githubusercontent.com/tpope/vim-pathogen/master/autoload/pathogen.vim > pathogen.vim
        cd ~/.vim/bundle
        git clone git://github.com/scrooloose/nerdtree.git
```

## Fixes needed or commom errors

- Follow the next [steps for Yabai](https://github.com/koekeishiya/yabai/wiki/Installing-yabai-%28from-HEAD%29). Remember you have to disable SIP protection.
- Check [SIP disable instructions](https://github.com/koekeishiya/yabai/wiki/Disabling-System-Integrity-Protection)
- In VSCode you should start using the font "Menlo For Powerline" in your terminal to prevent issues with Oh My Zsh theme
- Needed to fix some compinint errors. See [this](https://stackoverflow.com/questions/13762280/zsh-compinit-insecure-directories) for more details.

```bash
          sudo chmod -R 755 /usr/local/share/zsh
          sudo chown -R root:staff /usr/local/share/zsh
```

- See the following regarding how to configure right fonts for iTerm [How to Configure your macOs Terminal with Zsh like a Pro](https://www.freecodecamp.org/news/how-to-configure-your-macos-terminal-with-zsh-like-a-pro-c0ab3f3c1156/)
- Follow tutorial: [Only steps after install NVM with Brew](https://jamesauble.medium.com/install-nvm-on-mac-with-brew-adb921fb92cc). After that use nvm install --lts to get the latest lts version.
- Follow tutorial: [Install NativeScript](https://docs.nativescript.org/start/ns-setup-os-x)
- Follow tutorial: [Install NX](https://nx.dev/latest/angular/getting-started/cli-overview#installing-the-cli)
- Follow the instructions at [this](https://formulae.brew.sh/cask/google-cloud-sdk) in order to get gcloud available on the terminal
