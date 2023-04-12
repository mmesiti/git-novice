---
layout: page
title: SSH Key Setup 
---

Before we can connect to a remote repository on GitHub
we need to set up a way for our computer to authenticate 
with GitHub so it knows it’s us trying to connect 
to our remote repository. 

We are going to set up the method that is commonly used by many different services to authenticate access on the command line.  This method is called Secure Shell Protocol (SSH).  SSH is a cryptographic network protocol that allows secure communication between computers using an otherwise insecure network.  

SSH uses what is called a key pair. This is two keys that work together to validate access. One key is publicly known and called the public key, and the other key called the private key is kept private. Very descriptive names.

You can think of the public key as a padlock, and only you have the key (the private key) to open it. You use the public key where you want a secure method of communication, such as your GitHub account.  You give this padlock, or public key, to GitHub and say “lock the communications to my account with this so that only computers that have my private key can unlock communications and send git commands as my GitHub account.”  

What we will do now is the minimum required to set up the SSH keys and add the public key to a GitHub account.

> ## Advanced SSH
> A supplemental episode in this lesson discusses SSH and key pairs in more depth and detail. 
{: .callout}

The first thing we are going to do is check if this has already been done on the computer you’re on.  Because generally speaking, this setup only needs to happen once and then you can forget about it. 

> ## Keeping your keys secure
> You shouldn't really forget about your SSH keys, since they keep your account secure. It’s good 
>  practice to audit your secure shell keys every so often. Especially if you are using multiple 
>  computers to access your account.
{: .callout}

We will run the list command to check what key pairs already exist on your computer.

~~~
ls -al ~/.ssh
~~~
{: .language-bash}

Your output is going to look a little different depending on whether or not SSH has ever been set up on the computer you are using. 

Dracula has not set up SSH on his computer, so his output is 

~~~
ls: cannot access '/c/Users/Vlad Dracula/.ssh': No such file or directory
~~~
{: .output}

If SSH has been set up on the computer you're using, the public and private key pairs will be listed. The file names are either `id_ed25519`/`id_ed25519.pub` or `id_rsa`/`id_rsa.pub` depending on how the key pairs were set up.  
Since they don’t exist on Dracula’s computer, he uses this command to create them. 

## 1. Create an SSH key pair
To create an SSH key pair Vlad uses this command, where the `-t` option specifies which type of algorithm to use and `-C` attaches a comment to the key (here, Vlad's email):  

~~~
$ ssh-keygen -t ed25519 -C "vlad@tran.sylvan.ia"
~~~
{: .language-bash}

If you are using a legacy system that doesn't support the Ed25519 algorithm, use:
`$ ssh-keygen -t rsa -b 4096 -C "your_email@example.com"`

~~~
Generating public/private ed25519 key pair.
Enter file in which to save the key (/c/Users/Vlad Dracula/.ssh/id_ed25519):
~~~
{: .output}

We want to use the default file, so just press <kbd>Enter</kbd>.

~~~
Created directory '/c/Users/Vlad Dracula/.ssh'.
Enter passphrase (empty for no passphrase):
~~~
{: .output}

Now, it is prompting Dracula for a passphrase.  Since he is using his lab’s laptop that other people sometimes have access to, he wants to create a passphrase.  Be sure to use something memorable or save your passphrase somewhere, as there is no "reset my password" option. 

~~~
Enter same passphrase again:
~~~
{: .output}

After entering the same passphrase a second time, we receive the confirmation

~~~
Your identification has been saved in /c/Users/Vlad Dracula/.ssh/id_ed25519
Your public key has been saved in /c/Users/Vlad Dracula/.ssh/id_ed25519.pub
The key fingerprint is:
SHA256:SMSPIStNyA00KPxuYu94KpZgRAYjgt9g4BA4kFy3g1o vlad@tran.sylvan.ia
The key's randomart image is:
+--[ED25519 256]--+
|^B== o.          |
|%*=.*.+          |
|+=.E =.+         |
| .=.+.o..        |
|....  . S        |
|.+ o             |
|+ =              |
|.o.o             |
|oo+.             |
+----[SHA256]-----+
~~~
{: .output}

The "identification" is actually the private key. You should never share it.  The public key is appropriately named.  The "key fingerprint" 
is a shorter version of a public key.

Now that we have generated the SSH keys, we will find the SSH files when we check.

~~~
ls -al ~/.ssh
~~~
{: .language-bash}

~~~
drwxr-xr-x 1 Vlad Dracula 197121   0 Jul 16 14:48 ./
drwxr-xr-x 1 Vlad Dracula 197121   0 Jul 16 14:48 ../
-rw-r--r-- 1 Vlad Dracula 197121 419 Jul 16 14:48 id_ed25519
-rw-r--r-- 1 Vlad Dracula 197121 106 Jul 16 14:48 id_ed25519.pub
~~~
{: .output}

## 2. Copy the public key to GitHub
Now we have a SSH key pair and we can run this command to check if GitHub can read our authentication.  

~~~
ssh -T git@github.com
~~~
{: .language-bash}


~~~
The authenticity of host 'github.com (192.30.255.112)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? y
Please type 'yes', 'no' or the fingerprint: yes
Warning: Permanently added 'github.com' (RSA) to the list of known hosts.
git@github.com: Permission denied (publickey).
~~~
{: .output}

Right, we forgot that we need to give GitHub our public key!  

First, we need to copy the public key.  Be sure to include the `.pub` at the end, otherwise you’re looking at the private key. 

~~~
cat ~/.ssh/id_ed25519.pub
~~~
{: .language-bash}

~~~
ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDmRA3d51X0uu9wXek559gfn6UFNF69yZjChyBIU2qKI vlad@tran.sylvan.ia
~~~
{: .output}

Now, going to GitHub.com, click on your profile icon in the top right corner to get the drop-down menu.  Click "Settings," then on the 
settings page, click "SSH and GPG keys," on the left side "Account settings" menu.  Click the "New SSH key" button on the right side. Now, 
you can add the title (Dracula uses the title "Vlad's Lab Laptop" so he can remember where the original key pair
files are located), paste your SSH key into the field, and click the "Add SSH key" to complete the setup.

Now that we’ve set that up, let’s check our authentication again from the command line. 
~~~
$ ssh -T git@github.com
~~~
{: .language-bash}

~~~
Hi Vlad! You've successfully authenticated, but GitHub does not provide shell access.
~~~
{: .output}

Good! This output confirms that the SSH key works as intended. We are now ready to push our work to the remote repository.

