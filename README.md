# Disaster-Tweet-Classification-using-BERT

## Deployment Steps : 
   - **Update Ubuntu**
      -  sudo apt update
      -  sudo apt install python3-pip python3-dev build-essential libssl-dev libffi-dev python3- setuptools
      
   - **Install Python Virtual Environment**
      -  sudo apt install python3-venv

   - **Make a parent directory for our project**
      - mkdir ~/bertmodel
      - cd ~/bertmodel
      - project directory: bertmodel
      - virtual environment: bertenv username: ubuntu
      - server_ip: EC2 Instance Public IP default http port: 80
      - flask port: 5000 
   
   - **Create a Virtual Environment**
      - python -m venv bertenv
        - This will install local copy of Python packages into virtual environment
      - Activate virtual environment [source bertenv/bin/activate]

   - **Setting up Flask Application**
      - pip install wheel
      - pip install wheel
      - sudo ufw allow 5000
      
   - **Setting up uWSGI**
      - We need to create a wsgi.py file inside the bertmodel project dir.
        nano ~/bertmodel/wsgi.py
      - Inside wsgi.py file add below code<br>
        from bertmodel import app<br>
        if __name__ == "__main__": app.run()
       
   -  **Testing uWSGI**
      - uwsgi --socket 0.0.0.0:5000 --protocol=http -w wsgi:app
      - http://public_ip:5000

   - **Configuring uWSGI**
      - nano ~/bertmodel/bertmodel.ini 
      - Content to add in ini file.<br><br>
         [uwsgi]<br>
         module = wsgi:app<br>
         <br>
         master = true <br>
         processes = 5<br>
         <br>
         socket = bertmodel.sock <br>
         chmod-socket = 660 <br>
         vacuum = true<br>
         <br>
         die-on-term = true<br>
         
   - **Creating systemd Unit File**
      - sudo nano /etc/systemd/system/bertmodel.service
      - Content <br>
         [Unit]<br>
         Description=uWSGI instance to serve bertmodel <br>
         After=network.target<br>
         <br>
         [Service]<br>
         User=ubuntu<br>
         Group=www-data<br>
         WorkingDirectory=/home/ubuntu/bertmodel <br>
         Environment="PATH=/home/ubuntu/bertmodel/bertenv/bin" <br>
         ExecStart=/home/ubuntu/bertmodel/bertenv/bin/uwsgi --ini bertmodel.ini<br>
         <br>
         [Install] WantedBy=multi-user.target<br>
         
   - **Start uWSGI services**
      -  sudo systemctl start bertmodel
      -  sudo systemctl enable bertmodel
      -  sudo systemctl start bertmodel
        
   - **Installing NGINX**
      -  sudo apt update
      -  sudo apt install nginx
      -  sudo ufw app list
      -  sudo ufw allow 'Nginx HTTP'
      -  sudo ufw status
      -  systemctl status nginx
      -  Check your NGINX is working?<br>
          http://public_ip
   
   - **Managing NGINX process**
      -  sudo systemctl stop nginx
      -  sudo systemctl start nginx
      -  sudo systemctl restart nginx
      -  sudo systemctl reload nginx
     
   - **Configuring NGINX**
      - sudo nano /etc/nginx/sites-available/bertmodel
      - Content <br>
        server {
            listen 80;<br>
            server_name bertmodel public_ip;<br>
            location / {<br>
               include uwsgi_params;<br>
               uwsgi_pass unix:/home/ubuntu/bertmodel/bertmodel.sock;<br>
            } <br>
         }<br>
      - To enable the Nginx server block configuration.<br>
        sudo ln -s /etc/nginx/sites-available/bertmodel /etc/nginx/sites-enabled<br>
        sudo nginx -t<br>
        sudo systemctl restart nginx<br>
        sudo ufw delete allow 5000<br>
        sudo ufw allow 'Nginx Full'<br>
        http://public-ip<br>
     
   - **Errors checking**
      - sudo less /var/log/nginx/error.log
      - sudo less /var/log/nginx/access.log
      - sudo journalctl -u nginx
      - sudo journalctl -u bertmodel
      


   
