# SSH Keys with Multiple GitHub Accounts
Multi SSH key

#### Creating SSH Keys
If you already have multiple SSH keys, you probably won’t need to make new ones. However, you might need to register them with ssh-agent if you haven’t done so already. If you are having problems, creating a new SSH key is quick and easy to do.

Viewing Existing Keys
You can view all of the existing SSH keys at ~/.ssh/

If you’ve followed SSH key creation steps before, you’ll likely have a file called id_rsa and another called id_rsa.pub; these are your private and public keys, respectively.

If you already have all of the SSH keys you want, you can skip the creation steps. Otherwise, read on for instructions for creating one or more new keys.

**Creating a New SSH Key**
You create a new key by running the following command, substituting for your own email address:

```bash
$ ssh-keygen -t rsa -b 4096 -C “your_email@example.com”
```

You’ll be prompted to “Enter file in which to save the key”. You should use the standard id_rsa prefix followed by a descriptive name for your key.

So let’s assume you need to make accounts for a personal SSH key and a work one.

Call the first key id_rsa_personal
Repeat the process again to create id_rsa_work
⚠️ Note that the prompt expects you to enter the path to the file as well; failing to add this will create the file in the current terminal directory which may not be what you want.

The final step is to secure the keys with a passphrase. Do so.

**Registering your new SSH Keys**
New keys need registered before they are useful. To register, we need to use ssh-agent and to use that, we need to first make sure it is running:

```bash
$ eval "$(ssh-agent -s)"
```

Once running, you can then add your new keys. Repeat the following command for all new keys you have added, substituting the correct path to the private key. For example:

```bash
$ ssh-add ~/.ssh/id_rsa_personal
```

Add your SSH Keys to GitHub.com
You can follow the GitHub Documentation in case this process changes on their website. Currently, you want to copy your public key and paste it into the relevant section on your GitHub settings.

To copy the public key to the clipboard, run the following, substituting for the correct filename:

```bash
$ pbcopy < ~/.ssh/id_rsa_personal.pub
```

⚠️ Note, you want to be really careful that you match up the correct key with the correct GitHub account otherwise you are in for a world of confusion. You see now why I recommend descriptive names for them?

When signed in to GitHub.com (with the right account!) click on your avatar and choose settings. Choose SSH and GPG Keys from the menu, and click the New SSH Key button.

Registering an SSH Key on GitHub.com

```bash
$ touch ~/.ssh/config
```

Here is an example from my `~/.ssh/config` file

```bash
# Personal SSH key
Host me.github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa_personal

# Standard SSH key
Host github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa
```

```bash
$ git@github.com:USERNAME/PROJECT.git
```

```bash
# Personal SSH key
Host me.github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/id_rsa_personal
```

**uses standard SSH key**
```bash
$ git clone git@github.com:CDRussell/SurvivingPresenters.git
```

**uses personal SSH key**
```bash
$ git clone git@me.github.com:CDRussell/SurvivingPresenters.git
```
