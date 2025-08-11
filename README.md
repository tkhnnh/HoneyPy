![HONEPY-Logo](/assets/images/honeypy-logo-black-text.png)

A modular, graphic-based honeypot to capture IP Adresses, usernames, passwords, and commands from various protocols (SSH & HTTP supported right now). Written in Python.

# Install

**1) Clone repository.**
`git clone https://github.com/collinsmc23/ssh_honeypy.git`

**2) Permissions.**
Move into `ssh_honeypy` folder.

Ensure `main.py` has proper permisions. (`chmod 755 honeypy.py`)

**3) Keygen.**

Create a new folder `static`. 

`mkdir static`

Move into directory.

`cd static`

An RSA key must be generated for the SSH server host key. The SSH host key provides proper identification for the SSH server. Ensure the key is titled `server.key` and resides in the same relative directory to the main program.

`ssh-keygen -t rsa -b 2048 -f server.key`
