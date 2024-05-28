/home/admin/.miniconda3/envs/jupyter_server/bin/jupyter-lab --no-browser --ip=0.0.0.0 ~/.jupyter_workspace


[Unit]
Description=Jupyter
After=syslog.target network.target

[Service]
User=admin
Environment="PATH=/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/home/admin/.local/bin"
WorkingDirectory=/home/admin/.jupyter_workspace/
ExecStart=/home/admin/.miniconda3/envs/jupyter_server/bin/jupyter-lab --ip=0.0.0.0 --no-browser

Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
