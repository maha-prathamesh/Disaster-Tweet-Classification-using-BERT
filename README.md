# Disaster-Tweet-Classification-using-BERT

## Deployment Steps : 
   - Update Ubuntu
      -  sudo apt update
      -  sudo apt install python3-pip python3-dev build-essential libssl-dev libffi-dev python3- setuptools
      
   - Install Python Virtual Environment
      -  sudo apt install python3-venv

   - Make a parent directory for our project
      - mkdir ~/bertmodel
      - cd ~/bertmodel
      - project directory: bertmodel
      - virtual environment: bertenv username: ubuntu
      - server_ip: EC2 Instance Public IP default http port: 80
      - flask port: 5000 
   
   - Create a Virtual Environment
      - python -m venv bertenv
        - This will install local copy of Python packages into virtual environment
      - Activate virtual environment [source bertenv/bin/activate]

   - Setting up Flask Application
      - pip install wheel
      - pip install wheel
      - sudo ufw allow 5000
      
   - Setting up uWSGI
      - We need to create a wsgi.py file inside the bertmodel project dir.
        nano ~/bertmodel/wsgi.py
      - Inside wsgi.py file add below code<br>
        from bertmodel import app<br>
        if __name__ == "__main__": app.run()
       
   -  Testing uWSGI
      - uwsgi --socket 0.0.0.0:5000 --protocol=http -w wsgi:app
      - http://public_ip:5000

   - Configuring uWSGI
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
        

