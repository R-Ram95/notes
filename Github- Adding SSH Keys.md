---
tags:
  - Github
  - Guide
---
# What are SSH Keys?

SSH - Secure Shell or Secure Socket Shell

Process to create a secure connection between two devices using public/private key pair. You can read and write data in github repos using SSH. When you connect via ssh, you authenticate using a `private key` on your local machine which you add to the SSH Agent. You add a `public key` to your github account.

  

The ssh agent manages your ssh keys and makes it so that you don’t need to enter a passphrase every time you ssh.

# Check for Existing SSH Keys

1) List files in `.ssh` directory enter `ls -al ~/.ssh` in terminal

2) check directory for:

- id_rsa.pub
- id_ecdsa.pub
- id_ed25519.pub

  

# Generate new SSH and add to ssh-agent

## Generate new SSH Keys

1) enter `$ ssh-keygen -t ed25519 -C "your_email@example.com"` in the terminal using your Github Email

this generates a new SSH using the provided email as the label

2) Press Enter to accept default location ⇒ if asked to rewrite keys, you can type the default file location and replace `id_ssh_keyname` with custom key name

3) type secure pass phrase

## Add key to SSH Agent

When adding new keys, use the `ssh-add` command

1) start ssh-agent in background

`eval "$(ssh-agent -s)"`

2) Add SSH private key to ssh-agent and store pass phrase in the keychain

`$ ssh-add --apple-use-keychain ~/.ssh/id_ed25519`

## Add SSH key to Github Account

1) Copy the public ssh key to clipboard ⇒ should be the file that ends with `.pub`

`$ pbcopy < ~/.ssh/id_ed25519.pub`

2) Profile > settings > Access > SSH and GPG keys

3) Click new SSH key

4) Paste key in the field

# References

> [!info] Adding a new SSH key to your GitHub account - GitHub Docs  
> You can access and write data in repositories on GitHub.  
> [https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/adding-a-new-ssh-key-to-your-github-account)  

> [!info] What is Secure Shell or SSH? How does it work?  
> Closed.  
> [https://stackoverflow.com/questions/31226188/what-is-secure-shell-or-ssh-how-does-it-work](https://stackoverflow.com/questions/31226188/what-is-secure-shell-or-ssh-how-does-it-work)