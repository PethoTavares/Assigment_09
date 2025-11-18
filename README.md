Assignment 9 – Cisco DNA Center Automation (Django + MongoDB)
Overview

This project implements a Django web application that interacts with Cisco DNA Center APIs and stores activity logs in a MongoDB database. The system provides a simple web interface to authenticate with DNAC, list network devices, and display interface details for a selected device. All actions are logged in MongoDB with timestamp, IP, and status.

Features
1. Cisco DNA Center Authentication

URL: /auth/

Retrieves an authentication token from Cisco DNAC

Displays the token in the browser

Logs the result in MongoDB

2. Device List

URL: /devices/

Fetches all network devices from DNAC

Renders results in an HTML table

Logs the request in MongoDB

3. Interface Details

URL: /interfaces/<ip_address>/

Shows interface details for a specific device IP

Logs the request in MongoDB

Project Structure
assignment9/
│── assignment9/
│   ├── settings.py
│   ├── urls.py
│   ├── wsgi.py
│── dna_center_cisco/
│   ├── templates/dna_center_cisco/
│   │   ├── base.html
│   │   ├── auth.html
│   │   ├── devices.html
│   │   ├── interface_details.html
│   ├── dnac_manager.py
│   ├── dnac_config.py
│   ├── urls.py
│   ├── views.py
│── db.sqlite3
│── manage.py
│── requirements.txt

Requirements

The main dependencies are listed in requirements.txt.
They include:

Django==4.2.26
requests
pymongo
dnac_config


Install everything with:

pip install -r requirements.txt

MongoDB Setup (EC2)

The MongoDB instance runs on a separate EC2 instance using Docker.

Commands used during setup:

sudo yum update -y
sudo amazon-linux-extras install docker -y
sudo systemctl start docker
sudo systemctl enable docker

docker run -d \
  -p 27017:27017 \
  -v ~/mongo_data:/data/db \
  --name mongodb \
  mongo


To verify:

sudo ss -tulnp | grep 27017


To enter MongoDB shell:

docker exec -it mongodb mongosh

Django Setup (WebServer-EC2)

Inside the web server EC2 instance:

sudo yum update -y
python3 -m venv env
source env/bin/activate
pip install --upgrade pip
pip install -r requirements.txt


Run migrations:

python manage.py migrate


Start Django:

python manage.py runserver 0.0.0.0:8000

Environment Configuration

Edit dnac_config.py with your Cisco DNAC credentials:

DNAC = {
    "BASE_URL": "https://sandboxdnac2.cisco.com",
    "USERNAME": "devnetuser",
    "PASSWORD": "Cisco123!",
    "VERIFY_SSL": False
}


Edit MongoDB config inside views.py:

client = MongoClient("mongodb://<mongo-public-ip>:27017/")
db = client["dnac_logs"]
collection = db["requests"]

How to Use

Navigate to the public IP of your Django server:

http://<webserver-ip>:8000/auth/


Authenticate and view the DNAC token.

Open the device list:

http://<webserver-ip>:8000/devices/


View interface details for a specific device by clicking the link in the table.

Check MongoDB logs:

docker exec -it mongodb mongosh
use dnac_logs
db.requests.find().pretty()

Screenshots Required (for the assignment)

Include the following when submitting:

Cisco DNAC authentication token displayed in the browser

Device list page rendered by Django

Interface details page for at least one device

Django running on the EC2 public IP in a browser

MongoDB shell showing logged entries

Notes

Security groups must allow:

HTTP (80), Django (8000), SSH (22)

MongoDB port 27017 only from the webserver IP

Debug mode should remain enabled during development only.
