---
title:  "Fix: Stop using root for WSL2"
categories: tech
tags:
  - wsl
  - security
---

I've been a big fan of WSL since it first came out, and now use [WSL2](https://docs.microsoft.com/en-us/windows/wsl/install-win10) ubuntu for most of my development activities either at home or at work.

However when I first setup it up I was a bit lazy and ended up using root by default. 

Similar to AWS, you should never really use a root user except to create a true admin user for your day to day activity. From the admin user you can always `sudo` whenever you need the elevated privileges. This guide summmarises the steps and resources I used to resolve this security malpractice quickly and easily.

## Step-by-step

This guide is targetting my Ubuntu 18.04.5 LTS distro
{: .notice--info}

#### Login to WSL2 as root

For me this just requires `wsl`, as root was my default user.

#### Create your new user

To add a 'matt' user, simply `adduser matt`

Enter a password and personal details as needed.

#### Promote to admin (sudo)

Add the user to the sudo group `usermod -aG sudo matt`

#### Filesystem migration (optional)

If like me you've been living in root for too long, you might want to move files and folders across your new user's home.

You can either do this from root directly, which is what I did, but you can also now do it from your new admin account using sudo.

You may want to consider for example:
 - .aws config
 - .bashrc
 - anything under your home '~' folder
 - Other configs

Once you've moved all the folders you'll want to change ownership (chown) to your new user `sudo chown -R matt PATH`

#### Change the WSL default user

Exit back into your windows command prompt. To change the user depends on your distro, but for me the following did the job `ubuntu config --default-user matt`

#### All done!

You now have a safe user to use for day to day, and can ignore root unless you ever really need it!

Sources:
 - [How To Create a Sudo User on Ubuntu](https://www.digitalocean.com/community/tutorials/how-to-create-a-new-sudo-enabled-user-on-ubuntu-18-04-quickstart)