



For the Windows server:

1. Open the Remote Desktop Connection (RDP) client and connect to your Windows server.
2. Open the 'Microsoft Store' and search for 'Windows Terminal'. Install and open the Windows Terminal as admin.
3. In the Windows Terminal, run the following command to open the SSH configuration file:
   ```
   notepad.exe %UserProfile%\.ssh\authorized_keys
   ```
   If the `.ssh` folder doesn't exist, create it first.
4. Copy the contents of your Linux client's `id_rsa.pub` file (the public key) and paste it into the `authorized_keys` file.
5. Save the `authorized_keys` file and close Notepad.

Now, you should be able to connect to your Windows server using SSH from your Linux client. Here's how:

1. In the Linux terminal, run the following command:
   ```
   ssh username@windows_server_ip
   ```
   Replace `username` with the appropriate user account on the Windows server, and `windows_server_ip` with the IP address or hostname of your Windows server.
2. If you set a passphrase for your SSH key, you will be prompted to enter it. If not, you should be logged in automatically.

That's it! You have now set up SSH access from your Linux client to your Windows server using an SSH key pair. This method is more secure than using a password-based authentication, as it eliminates the need to remember and manage complex passwords.


### Install OpenSSH-Server in WSL

First, install OpenSSH server inside your Linux Distro:

sudo apt install openssh-server  

##

## For Linux Client and Windows Server

### On linux Client

#### On the Linux client

1. Open the terminal on your Linux machine.
2. Check if you already have an SSH key pair by running the following command: ``` ls -al ~/.ssh ```

   If you see files named `id_rsa` and `id_rsa.pub`, you already have a key pair.
3. If you don't have a key pair, you can generate one by running the following command: ``` ssh-keygen -t rsa -f ~/.ssh/id_rsa ```

   This will create a 4096-bit RSA key pair and associate it with the email address you provided.

4. When prompted, choose a location to save the key files and enter a passphrase (optional) or just press ENTER to skip passphrase.
5. Your public key (`id_rsa.pub`) is now ready to be copied to the Windows server.
6. cat ~/.ssh/id_rsa.pub
7. copy the pub key (no whitespaces)

#### On the Windows server

1. open powershell as admin
2. In the Terminal, run the following command to open the SSH configuration file:

```bash
 notepad.exe %UserProfile%\.ssh\authorized_keys
```

 If the `.ssh` folder doesn't exist, create it first.

Copy the contents of your Linux client's `id_rsa.pub` file (the public key) and paste it into the `authorized_keys` file.
Save the `authorized_keys` file and close Notepad.
Run the command below. The output shows `True` when you're a member of the built-in Administrators group.

```bash
(New-Object Security.Principal.WindowsPrincipal([Security.Principal.WindowsIdentity]::GetCurrent())).IsInRole([Security.Principal.WindowsBuiltInRole]::Administrator)
```

1. If the output shows `True` you are an admin user. Copy the public key to administrators_authorized_keys:

   `cp %UserProfile%\.ssh\authorized_keys "$env:programdata\ssh\administrators_authorized_keys"`

   or

   `cp $env:USERPROFILE\.ssh\authorized_keys "$env:programdata\ssh\administrators_authorized_keys"` 

    _Please note administrators_authorized_keys is the file name without any extension.

#### set the right Windows ACL Permissions using one of the host private key file.
    
    `get-acl "$env:programdata\ssh\ssh_host_rsa_key" | set-acl "$env:programdata\ssh\administrators_authorized_keys"`
    
    _If you don't have .ssh folder under $env:USERPROFILE folder then manually create it_

### From Your Linux Client Machine

ssh your-windows-username@you-windows-pc-name  (gets you into the windows machine)
ssh your-wsl-username@ip (gets you into the wsl-linux environment


    
## for Windows client and Linux Server

Here are the steps to create an SSH key pair and set up SSH access on a Windows client and a Linux server:

### On the Windows Client

1. Open PowerShell or Command Prompt as an administrator.

2. Generate an SSH key pair:

```
ssh-keygen -t rsa -b 4096 -f %USERPROFILE%\.ssh\id_rsa
```

This will create the private key `id_rsa` and the public key `id_rsa.pub` in the `%USERPROFILE%\.ssh` directory.

3. Copy the contents of the public key file `%USERPROFILE%\.ssh\id_rsa.pub`.

### On the Linux Server

1. Open a terminal and navigate to the `/home/username/.ssh` directory, where `username` is the name of the user you want to grant SSH access to.

2. If the `.ssh` directory doesn't exist, create it:

```
mkdir ~/.ssh
```

3. Create or edit the `authorized_keys` file and paste the content of the public key you copied from the Windows client:

```
nano ~/.ssh/authorized_keys
```

4. Save the file and exit the text editor.

5. Set the correct permissions for the `.ssh` directory and the `authorized_keys` file:

```
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

Now, the Linux server is configured to accept SSH connections from the Windows client using the provided SSH key pair.

To connect from the Windows client to the Linux server using the SSH key, use the following command:

```
ssh -i %USERPROFILE%\.ssh\id_rsa username@linux_server_ip
```

Replace `username` with the username on the Linux server and `linux_server_ip` with the IP address or hostname of the Linux server.

This setup allows you to connect to the Linux server from the Windows client without using a password, as the SSH key is used for authentication.