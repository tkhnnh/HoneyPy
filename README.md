![HONEPY-Logo](/assets/images/honeypy-logo-black-text.png)

A modular, graphic-based honeypot to capture IP Adresses, usernames, passwords, and commands from various protocols (SSH & HTTP supported right now). Written in Python.

# Install

**1) Clone repository.**
`git clone https://github.com/tkhnnh/HoneyPy.git`

**2) Permissions.**
Move into `ssh_honeypy` folder.

Ensure `main.py` has proper permisions. (`chmod 755 honeypy.py`)

**3) Keygen.**

Create a new folder `honeykey`. 

`mkdir honeykey`

Move into directory.

`cd honey`

An RSA key must be generated for the SSH server host key. The SSH host key provides proper identification for the SSH server. Ensure the key is titled `server.key` and resides in the same relative directory to the main program.

`ssh-keygen -t rsa -b 2048 -f server.key`

# Usage

To provision a new instance of HONEYPY, use the `honeypy.py` file. This is the main file to interface with for HONEYPY. 

HONEYPY requires a bind IP address (`-a`) and network port to listen on (`-p`). Use `0.0.0.0` to listen on all network interfaces. The protocol type must also be defined.

```
-a / --address: Bind address.
-p / --port: Port.
-s / --ssh OR -wh / --http: Declare honeypot type.
```

Example: `python3 honeypy.py -a 0.0.0.0 -p 22 --ssh`

💡 If HONEPY is set up to listen on a privileged port (22), the program must be run with `sudo` or root privileges. No other services should be using the specified port. 

If port 22 is being used as the listener, the default SSH port must be changed. Refer to Hostinger's "[How to Change the SSH Port](https://www.hostinger.com/tutorials/how-to-change-ssh-port-vps)" guide.

❗ To run with `sudo`, the `root` account must have access to all Python libraries used in this project (libraries defined in `requirements.txt`). Install by switching to the root account, then supply:

`root@my_host# pip install -r requirements`

This will install all the packages for the root user, but it will affect the global environment and isn't considered the "safeest" way to do this.

**Optional Arguments**

A username (`-u`) and password (`-w`) can be specified to authenticate the SSH server. The default configuration will accept all usernames and passwords.

```
-u / --username: Username.
-w / --password: Password.
-t / --tarpit: For SSH-based honeypots, -t can be used to trap sessions inside the shell, by sending a 'endless' SSH banner.
```

Example: `python3 main.py -a 0.0.0.0 -p 22 --ssh -u admin -w admin --tarpit`

# Logging Files

HONEYPY has three loggers configured. Loggers will route to either `cmd_audits.log`, `creds_audits.log` (for SSH), and `http_audit.log` (for HTTP) log files for information capture.

`cmd_audits.log`: Captures IP address, username, password, and all commands supplied.

`creds_audits.log`: Captures IP address, username, and password, comma seperated. Used to see how many hosts attempt to connect to SSH_HONEYPY.

`http_audit.log`: Captures IP address, username, password.

The log files will be located in `../ssh_honeypy/log_files/..`

# Honeypot Types
This honeypot was written with modularity in mind to support future honeypot types (Telnet, HTTPS, SMTP, etc). As of right now there are two honeypot types supported.

## SSH
The project started out with only supported SSH. Use the following instructions above to provision an SSH-based honeypot which emulates a basic shell.

💡 `-t / --tarpit`: A tarpit is a security mechanism designed to slow down or delay the attempts of an attacker trying to brute-force login credentials. Leveraging Python's time module, a very long SSH-banner is sent to a connecting shell session. The only way to get out of the session is by closing the terminal. 

## HTTP
Using Python Flask as a basic template to provision a simple web service, HONEYPY impersonates a default WordPress `wp-admin` login page. Username / password pairs are collected.

There are default credentials accepted, `admin` and `deeboodah`, which will proceed to a Rick Roll gif. Username and password can be changed using the `-u / --username: Username.
-w / --password: Password` arguments.

The web-based honeypot runs on port 5000 by default. This can be changed using the `-p / --port` flag option.

💡 There is currently not a dashboard panel supported for HTTP-based results. This will be a future addition.

# Dashboard

HONEYPY comes packaged with a `web_honeypot.py` file. This can be run in a seperate terminal session on localhost to view statistics such as top 10 IP addresses, usernames, passwords, commands, and all data in tabular format. As of right now, the dashboards do not dynamically update as new entries or information are added to the log files. The dashboard must be run every time to re-populate to the most up-to-date information.

Run `python3 web_honeypot.py` on localhost. Default port for Python Dash is `8050`. `http://127.0.0.1:8050`. Go to your browser of choice to view dashboard metrics.

💡 The dashboard data includes a country code lookup that uses the IP address to determine the two-digit country code. To get the IP address, the [ipinfo() CleanTalk API](https://cleantalk.org/help/api-ip-info-country-code) is used. Due to rate limiting contraints, CleanTalk can only lookup 1000 IP addresses per 60 seconds. 
- By default, the country code lookup is set to `False`, as this will have impact on how long it takes to provision the honeypot (pandas has to pivot on dataframes, which takes time). Set the `COUNTRY` environment variable to `True` if you would like to get the country code lookup dashboard panel.
- If receiving rate limiting errors, change the `COUNTRY` environment variable in `public.env` to `False` again. 

HONEPY leverages Python Dash to populate the bar charts, Dash Bootstrap Components for dark-theme and style of charts, and Pandas for data parsing.

<img src="/assets/images/Dashboard.PNG" alt="Dashboard" width="600"/>