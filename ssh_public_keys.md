# Add your public SSH keys in the table (to be used for the remote connection from Laptop/Tablet to the Linux VM @server_bob)

1. Generate s ssk key pair (public & private key) on your local machine by typing via command line `ssh-keygen -t ed25519` or `ssh-keygen -t rsa -b 4096` (...the keys should look like something like this: `ssh-ed25519 .....== user@userPC`)
2. Add the content of your fresh generated public part of the ssh key-pair as pull-request to the table below
   
*Your IP address is always `172.20.1.X/24` with `X = 10 + <team number>`*
<br/>

**Choose & Commit**

| Team |  VM IP	        | Student Name             |  public ssh key          |
| :--: | :------------: | :----------------------: | :----------------------: |
| **INF group** ||||
| 1    | 172.20.1.11/24 | Tim Ziemann | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIBdProJezscUd35VYopih3bKNWTypeDQhjqANXAYJYzN bobkatze@Tims-MacBook-Air-2.local |
| "    | "              | Luca Kowitz | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIN5ov+gGkIrYKl7/1VOdplRSyW2zk8n+v/5DBMIfaEyk luca@Luca-Linux |
| "    | "              | "           | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIECo3HFAkkhMDwqDEsu/TqqLnL0MJRU5QYGbobqpRGOL luca@Luca-PC |
| 2    | 172.20.1.12/24 | Patrick | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICuge0ornJKrUQVNePyoViwRqMasDj+s7SKkMN1+BiLj |
| 3    | 172.20.1.13/24 | Robin Franke | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAII2HpmbDNa8vCSmp5MK0owcypDYKafaoTFT3FH2UjQuQ robin@LAPTOP-SGTQQ001 |
| 4    | 172.20.1.14/24 | Adrian Bernhardt | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIP5Uon0DLs9jfR3vnEJV86jt9+ssggzk6ZXrLm1pUqcP |
| 5    | 172.20.1.15/24 | Nico | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIJ1ELhHn4eAOY8RWBsTi7TaO787wh9lNbYvM6B6jUBx8 nico@LAPTOP-OI06AT3U |
| 5    | "              | Till | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIHUMAZ1iN1+nDxZR++lEckfoFg0ZeMds3+mQw12TSvwa ttaba@LAPTOP-TiRoTa |
| 6    | 172.20.1.16/24 | Kamola | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIB8Byttpe8+zUJzPhh17Ag1cL4zTxLRqX7yu+1bDtw/p User@WIN-ERAATN0G3SA |
| 7    | 172.20.1.17/24 | Marius | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIPkm3f7bfM6wWYXDdoxMlJjgP0TLemJL+OWslGl0bDFy mahek@LAPTOP-SK6VI5B4 |
| 10   | 172.20.1.20/24 | **reserved** | |
| **AEE/ET group** ||||
| 11   | 172.20.1.21/24 | Sven | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIEqPfqsUSUpsszEDubLVsxNrW9I/Tz+xoAVGiRKGPCQd sven@MacBook-Pro-von-Sven.local |
| 12   | 172.20.1.22/24 | Tristan | ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAAAgQC/zLB/gcNRwDWoqwFShzTlTY5N3pqpBfRdLq/u8x/ZfkaLHbu2HSyoUXfrPQ1W1pQ/uT7VMr1QKp5G/0S25M68OJcCIIaZmpUUADVWkuhARDk4a+A+wCROfxBSRoosuQVAN+VdgBrXO+gCH/27wjyaT5wmcHXKUqCw3WK00FvIHw== trist@YOGA |
| 13   | 172.20.1.23/24 | Johann | ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDPRYEqvjKAANfcB/2vgLDi4vTFRqL4fBhJDqWY8e4CpBJMmJo7chawCg5aD3F/p7/4YEV9taUgy0OC6jCcgFihmLRqUdec+F9oZECiTiSbDCxciXE0IdgBIBix7wwe5Wh7nbJGz4ypCJbKZ2VD40gzYnqI4eNXq+wyMA2HqDI03HjYsBDJ5yzY3UoUX5D5XZiYXLZKmazx/h3yk0FzAmzI0ujb+u1c39xYnQyLOfNUtRM0iHpBwKGJS20NPgf/5458Qnbo9ZPxdcKX+76T+PvqdNcQkow2Chho3Y/A8fYKEkiE9gYIV2IUQtk7zPSL0bKkqpNpNm+0U5Sl/q26QnWfrWQwBjmlEEM6qKYdd9mSHHu2o4TX/JRn8TcBAZ9xDSo/paBXbiu2JL3WR3Go8T+tGDkFqtxQUc2SWcr4A192rj9iRYPBm6atwoQWPpvlpdphITD3AQqEzzP/P0/ez6OOzz4zjRGZ6lagUBWBhCqxvyKyZ9zfDXeXSVuKbeB8X2k= johanns@LAPTOP-EN45B187 |
| 13   | 172.29.1.23/24 | Niclas | ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCrAyHzaeqPBw56ACSEZENlsYreSE8UpFbL1vNr4FVZnZIvXg7nkeij9StR5mq6KkbZgdLBZC/Yxg3Hb8E1D5gIbJfG75Wp157DCYlkCgOD2tWfJPcqqkBj6HikrQb0IIJgGb306Dl55LniETrSPhoMlonpiVPnXg5vwt9nJ8r1kcz4VNiiHcXPFWxztMf1voPy8TvjPeCQmSkg0sPksRt/9QOOP81hvqSE+a/Xlw1nIqnsWDMyg9+/ZakifI9JsQp9nR8Cp019bScq5bGTW7XVx16w7n2tSo0Kni11JUtbaE5deH6vtJ03I6MngEsuW6hKV9c9ee68x2RlUzYZzR9T5C9Vn1qq4LT2PzyFp3B59kxPfh3G/+RCuoFwCs3TXMUxqxIVuTx8GloQu4MTpS7lJ33lccYO7opdJtd3gc3PGjTc5nmM5G9eSLjtw8USb3WU3hrvxAMgG3q7k0hEC5pNuQBV6B+KOSWSu5gQgCVpEbrZJTZhGSXLwWH6cOPYLJ0= nicla@DESKTOP-R6PR6SB |
| 14   | 172.20.1.24/24 | Felix | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAINOBGK7qJVRWqpI6LnlRgjdiHTE7x80wBjT5SJye2aHf fschulze@penguin |
| 15   | 172.20.1.25/24 | Viktoria | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIGZDhQHfCGBzd6mlQqEBxKK4GHB74S2jFDVChuTUS75W |
| 15   | 172.20.1.25/24 | Jan | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIH2n5bEN1RSq6jVbCZTLm3i6O1kkX8EHHXY+Jf1ut65F |
| 16   | 172.20.1.26/24 | Nicson Andrade | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAICHaXIqOPS948FjDZ93jGCauLoTmMhZH/EcHwU56x4wZ venican@DESKTOP-UAC1QVL |
| 14   | 172.20.1.24/24 |  Tim | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIFu5gBWWqj0IrUFHh/fTG17W1w4+DAuAyhsqJZHDAwt7 |
| 16   | 172.20.1.26/24 | Ahmed Elgazzar | ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIOPML8letE9Hg2vnnD5ju6e4Q1CnsJCrazf0Egc3JzrW kali@kali |