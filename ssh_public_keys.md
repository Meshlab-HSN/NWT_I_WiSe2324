# Add your public SSH keys in the table (to be used for the remote connection from Laptop/Tablet to the Linux VM @server_bob)

1. Generate s ssk key pair (public & private key) on your local machine by typing via command line `ssh-keygen -t ed25519` or `ssh-keygen -t rsa -b 4096` (...the keys should look like something like this: `ssh-ed25519 .....== user@userPC`)
2. Add the content of your fresh generated public part of the ssh key-pair as pull-request to the table below


<br/>

**Choose & Commit**

|  Student Name	        |  Team		          	        | VM IP                    |  public ssh key          |
| :-------------------: | :-------------------------: | :----------------------: | :----------------------: |
| Tim Ziemann | 1 | 172.20.1.11/24 | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBdProJezscUd35VYopih3bKNWTypeDQhjqANXAYJYzN bobkatze@Tims-MacBook-Air-2.local |
| Patrick | 2 | 172.20.1.12/24 | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICuge0ornJKrUQVNePyoViwRqMasDj+s7SKkMN1+BiLj |
|  |  | 172.20.1.13/24 | |
|  |  | 172.20.1.14/24 | |
|  |  | 172.20.1.15/24 | |
|  |  | 172.20.1.16/24 | |
|  |  | 172.20.1.17/24 | |
|  |  | 172.20.1.18/24 | |
