# Up and running with JupyterHub 0.9.4 and Jupyter-LabHub
## JupyterHub 0.9.4
I am running it as a single user hub on my NVIDIA GEFORCE RTX 2080 server.

### Installing JupyterHub in the pytorch venv

```
conda activate pytorch
conda install jupyterhub
```

I am also using a self-signed certificate to encrypt the conection between my browser and the JupyterHub machine.
See [how to create and use a self-signed certificate with JupyterHub.](https://tikoehle.github.io/pytorch_conda_jupyterhub/certificates).

### Generate permanent JupyterHub config file

```
jupyterhub --generate-config
```

### Configure the location of the self-signed certificate (priv .key and pub .crt)

```
In jupyterhub_config.py:
c.JupyterHub.ssl_cert = '/etc/ssl/certs/jupyterhub.crt'
c.JupyterHub.ssl_key = '/etc/ssl/jupyterhub/jupyterhub.key'
c.Authenticator.admin_users = {'user'}
```

**Note: using `JupyterHub.bind_url` and setting port `443` did not work for me with JupyterHub 0.9.4.**

```
c.JupyterHub.bind_url = 'http://<ip_addr>:443'  # Keep the default port binding! This line throws an exception!
```

## jupyterlab-hub
Serve jupyterlab-hub (jupyterlab is the new jupyter notebook). This works out of the box with jupyterhub 0.9.4.

### Configure JupyterHub's Spawner to start `jupyter-labhub`

```
In jupyterhub_config.py:
#c.Spawner.cmd = ['jupyterhub-singleuser']
c.Spawner.cmd = ['jupyter-labhub']
```

## Start the JupyterLab-Hub
On the GPU server:

```
conda activate pytorch
jupyterhub
```

The client browser connects to port 8000.

```
https://<jupyterlab-hub>:8000
```

## Some additional knobs

### Disable excessive logging

```
In jupyterhub_config.py:

# Set the log level of jupyterhub.
# Application.log_level : 0|10|20|30|40|50|'DEBUG'|'INFO'|'WARN'|'ERROR'|'CRITICAL'
# Default: Informational (20)
c.Application.log_level = 20

# Disable debug-logging of the single-user server.
c.Spawner.debug = False

# Suppress all the API GET/PUT logs from the c.Spawner.cmd
c.Spawner.args=['--log-level=40']
```

## My final jupyterhub_config.py for jupyterhub 0.9.4 spawning 'jupyter-labhub'

```
c.Application.log_level = 20
c.JupyterHub.ssl_cert = '/etc/ssl/certs/jupyterhub.crt'
c.JupyterHub.ssl_key = '/etc/ssl/jupyterhub/jupyterhub.key'
c.Spawner.args=['--log-level=40']
c.Spawner.cmd = ['jupyter-labhub']
c.Spawner.debug = False
c.Authenticator.admin_users = {'user'}
c.PAMAuthenticator.open_sessions = False            # Dont issue a motd when opening .ipynb
```

## JupyterLab Extensions
In order to install JupyterLab extensions, you need to have Node.js installed.

```
conda install -c conda-forge nodejs
```

JupyterLab extensions can be installed manually using ```jupyter labextension ...``` commands, or, more convenially, 
using the JupyterLab 'Extension Manager'. The 'Extension Manager' extension (github/jupyterlab_discovery) is 
included in the core of JupyterLab but is disabled by default. To enable it:
- Go into Settings -> Advanced Settings Editor.
- Open the Extension Manager section.
- Under 'User Overrides', add the entry ```{"enabled": true}```.
- Save the settings and reload the page.

To search/browse/install any extensions, click on the 'Extension Manager' icon located in the left hand menue 
icon panel.
