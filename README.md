Problem Statement 1:

Title: Containerisation and Deployment of Wisecow Application on Kubernetes

Objective: To containerize and deploy the Wisecow application, hosted in the
above-mentioned GitHub repository, on a Kubernetes environment with secure TLS
communication.

Requirements:

Dockerization:
The first step is to create a Dockerfile for the Wisecow application. This Dockerfile defines the environment and dependencies required for the application to run.

Steps:
Create a Dockerfile in the root of the Wisecow application repository.
The Dockerfile should:
Use a base image, such as python:3.9-slim (assuming it's a Python app).
Install necessary dependencies using a requirements.txt file or similar.
Copy the application code into the container.
Expose a port (e.g., 8080) for the application to run.

code:-
# Dockerfile for Wisecow Application

# Step 1: Use an official Python base image
FROM python:3.9-slim

# Step 2: Set the working directory in the container
WORKDIR /app

# Step 3: Copy the requirements file and install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Step 4: Copy the rest of the application code
COPY . .

# Step 5: Expose the port on which the app runs
EXPOSE 8080

# Step 6: Define the command to run the application
CMD ["python", "app.py"]

 Kubernetes Deployment:

 To deploy the Wisecow application to Kubernetes, create YAML manifest files that define the application deployment, services, and necessary configurations.

Steps:
Create a directory named k8s/ to store Kubernetes manifest files.
Write the following manifest files:
Deployment Manifest (deployment.yaml): Defines how the application is deployed, including replicas, containers, and resource limits.
Service Manifest (service.yaml): Exposes the application for external access.

EXAMPLE:-deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: wisecow-deployment
  labels:
    app: wisecow
spec:
  replicas: 2
  selector:
    matchLabels:
      app: wisecow
  template:
    metadata:
      labels:
        app: wisecow
    spec:
      containers:
      - name: wisecow
        image: <your-registry>/wisecow:latest
        ports:
        - containerPort: 8080

EXAMPLE 2:- Service.yaml

apiVersion: v1
kind: Service
metadata:
  name: wisecow-service
spec:
  selector:
    app: wisecow
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: LoadBalancer

3. CI/CD Pipeline (GitHub Actions):

   Create a GitHub Actions workflow to automate Docker image building and deployment to Kubernetes whenever there are changes in the repository.

Steps:
Create a .github/workflows/deploy.yml file in the repository.
The workflow should:
Build the Docker image.
Push the image to a container registry (Docker Hub, GCP, AWS, etc.).
Apply the Kubernetes manifests to deploy the updated application.
Example GitHub Actions Workflow: deploy.yml

name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push Docker image
        run: |
          docker build -t <your-registry>/wisecow:latest .
          docker push <your-registry>/wisecow:latest

  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Set up kubectl
        uses: azure/setup-kubectl@v1
        with:
          version: 'v1.18.0'

      - name: Deploy to Kubernetes
        run: |
          kubectl apply -f k8s/deployment.yaml
          kubectl apply -f k8s/service.yaml

4. TLS Implementation:

   To secure the Wisecow application with TLS, use a tool like Cert-Manager to automatically provision and manage TLS certificates for Kubernetes.

Steps:
Install Cert-Manager in your Kubernetes cluster.
Use Let's Encrypt to generate a TLS certificate.
Create an Ingress resource to expose the application and apply the TLS certificate.
Example: ingress.yaml

apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: wisecow-ingress
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
spec:
  tls:
  - hosts:
      - wisecow.example.com
    secretName: wisecow-tls
  rules:
  - host: wisecow.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: wisecow-service
            port:
              number: 80

Expected Artifacts:

GitHub repository containing:

Source code of the Wisecow application.
Dockerfile.
Kubernetes manifest files (deployment.yaml, service.yaml, ingress.yaml).
GitHub Actions CI/CD pipeline (deploy.yml).
Container registry: The Docker image for the Wisecow application should be hosted here.

TLS configuration: Ensure secure communication via HTTPS using the TLS certificate.


Objective: Containerization and Deployment of the Wisecow Application on Kubernetes with TLS and CI/CD Pipeline
This guide outlines the steps required to containerize the Wisecow application, deploy it on a Kubernetes environment, automate continuous integration and deployment (CI/CD), and ensure secure communication using TLS.

Key Deliverables:
Dockerization:
Create a Dockerfile for the Wisecow application.
Kubernetes Deployment:
Develop Kubernetes manifest files for deploying the application.
Expose the Wisecow app as a Kubernetes service.
CI/CD Pipeline (using GitHub Actions):
Automate Docker image builds and pushes to a container registry.
Deploy the application to Kubernetes automatically after successful image builds.
TLS Implementation:
Implement secure TLS communication for the application.
Steps Overview:
1. Dockerization:
The first step is to create a Dockerfile for the Wisecow application. This Dockerfile defines the environment and dependencies required for the application to run.

Steps:
Create a Dockerfile in the root of the Wisecow application repository.
The Dockerfile should:
Use a base image, such as python:3.9-slim (assuming it's a Python app).
Install necessary dependencies using a requirements.txt file or similar.
Copy the application code into the container.
Expose a port (e.g., 8080) for the application to run.
dockerfile
Copy code
# Dockerfile for Wisecow Application

# Step 1: Use an official Python base image
FROM python:3.9-slim

# Step 2: Set the working directory in the container
WORKDIR /app

# Step 3: Copy the requirements file and install dependencies
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

# Step 4: Copy the rest of the application code
COPY . .

# Step 5: Expose the port on which the app runs
EXPOSE 8080

# Step 6: Define the command to run the application
CMD ["python", "app.py"]
2. Kubernetes Deployment:
To deploy the Wisecow application to Kubernetes, create YAML manifest files that define the application deployment, services, and necessary configurations.

Steps:
Create a directory named k8s/ to store Kubernetes manifest files.
Write the following manifest files:
Deployment Manifest (deployment.yaml): Defines how the application is deployed, including replicas, containers, and resource limits.
Service Manifest (service.yaml): Exposes the application for external access.
Example: deployment.yaml
yaml
Copy code
apiVersion: apps/v1
kind: Deployment
metadata:
  name: wisecow-deployment
  labels:
    app: wisecow
spec:
  replicas: 2
  selector:
    matchLabels:
      app: wisecow
  template:
    metadata:
      labels:
        app: wisecow
    spec:
      containers:
      - name: wisecow
        image: <your-registry>/wisecow:latest
        ports:
        - containerPort: 8080
Example: service.yaml
yaml
Copy code
apiVersion: v1
kind: Service
metadata:
  name: wisecow-service
spec:
  selector:
    app: wisecow
  ports:
    - protocol: TCP
      port: 80
      targetPort: 8080
  type: LoadBalancer
3. CI/CD Pipeline (GitHub Actions):
Create a GitHub Actions workflow to automate Docker image building and deployment to Kubernetes whenever there are changes in the repository.

Steps:
Create a .github/workflows/deploy.yml file in the repository.
The workflow should:
Build the Docker image.
Push the image to a container registry (Docker Hub, GCP, AWS, etc.).
Apply the Kubernetes manifests to deploy the updated application.
Example GitHub Actions Workflow: deploy.yml
yaml
Copy code
name: CI/CD Pipeline

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Set up Docker Buildx
        uses: docker/setup-buildx-action@v1

      - name: Login to Docker Hub
        uses: docker/login-action@v2
        with:
          username: ${{ secrets.DOCKER_USERNAME }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build and push Docker image
        run: |
          docker build -t <your-registry>/wisecow:latest .
          docker push <your-registry>/wisecow:latest

  deploy:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Set up kubectl
        uses: azure/setup-kubectl@v1
        with:
          version: 'v1.18.0'

      - name: Deploy to Kubernetes
        run: |
          kubectl apply -f k8s/deployment.yaml
          kubectl apply -f k8s/service.yaml
4. TLS Implementation:
To secure the Wisecow application with TLS, use a tool like Cert-Manager to automatically provision and manage TLS certificates for Kubernetes.

Steps:
Install Cert-Manager in your Kubernetes cluster.
Use Let's Encrypt to generate a TLS certificate.
Create an Ingress resource to expose the application and apply the TLS certificate.
Example: ingress.yaml
yaml
Copy code
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: wisecow-ingress
  annotations:
    cert-manager.io/cluster-issuer: "letsencrypt-prod"
spec:
  tls:
  - hosts:
      - wisecow.example.com
    secretName: wisecow-tls
  rules:
  - host: wisecow.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: wisecow-service
            port:
              number: 80
Update your DNS settings to point to the IP of the Kubernetes load balancer.
Cert-Manager will automatically issue and renew the TLS certificate using Let's Encrypt.
Expected Artifacts:
GitHub repository containing:

Source code of the Wisecow application.
Dockerfile.
Kubernetes manifest files (deployment.yaml, service.yaml, ingress.yaml).
GitHub Actions CI/CD pipeline (deploy.yml).
Container registry: The Docker image for the Wisecow application should be hosted here.

TLS configuration: Ensure secure communication via HTTPS using the TLS certificate.

Access Control:

Ensure the GitHub repository is public and accessible for verification and testing.

End Goal:
The successful containerization and deployment of the Wisecow application on Kubernetes, with a fully automated CI/CD pipeline using GitHub Actions and secure TLS communication.

Problem Statement 2:

1. System Health Monitoring Script:
   
Develop a script that monitors the health of a Linux system. It should check
CPU usage, memory usage, disk space, and running processes. If any of
these metrics exceed predefined thresholds (e.g., CPU usage > 80%), the
script should send an alert to the console or a log file.

code:-
import psutil
import logging
from datetime import datetime

# Configure logging to log alerts to a file
logging.basicConfig(
    filename="system_health.log",
    level=logging.INFO,
    format="%(asctime)s - %(levelname)s - %(message)s"
)

# Predefined thresholds
CPU_THRESHOLD = 80  # Percentage
MEMORY_THRESHOLD = 80  # Percentage
DISK_THRESHOLD = 80  # Percentage
MAX_PROCESSES = 150  # Number of processes

# Function to log and print alerts
def log_alert(message):
    print(message)
    logging.info(message)

# Function to monitor CPU usage
def check_cpu():
    cpu_usage = psutil.cpu_percent(interval=1)
    if cpu_usage > CPU_THRESHOLD:
        log_alert(f"High CPU usage detected: {cpu_usage}% (Threshold: {CPU_THRESHOLD}%)")
    else:
        log_alert(f"CPU usage: {cpu_usage}%")

# Function to monitor memory usage
def check_memory():
    memory = psutil.virtual_memory()
    memory_usage = memory.percent
    if memory_usage > MEMORY_THRESHOLD:
        log_alert(f"High memory usage detected: {memory_usage}% (Threshold: {MEMORY_THRESHOLD}%)")
    else:
        log_alert(f"Memory usage: {memory_usage}%")

# Function to monitor disk usage
def check_disk():
    disk = psutil.disk_usage('/')
    disk_usage = disk.percent
    if disk_usage > DISK_THRESHOLD:
        log_alert(f"Low disk space detected: {disk_usage}% used (Threshold: {DISK_THRESHOLD}%)")
    else:
        log_alert(f"Disk space usage: {disk_usage}%")

# Function to monitor running processes
def check_processes():
    processes = len(psutil.pids())
    if processes > MAX_PROCESSES:
        log_alert(f"Too many running processes: {processes} (Threshold: {MAX_PROCESSES})")
    else:
        log_alert(f"Number of running processes: {processes}")

# Main function to check system health
def monitor_system():
    log_alert("Starting system health monitoring...")
    check_cpu()
    check_memory()
    check_disk()
    check_processes()
    log_alert("System health check completed.\n")

# Schedule the monitoring at intervals (e.g., every 60 seconds)
if __name__ == "__main__":
    import time
    while True:
        monitor_system()
        time.sleep(60)  # Check every 60 seconds

Explanation:

Modules Used:

psutil: A cross-platform library that provides an interface for retrieving information on system utilization (CPU, memory, disk, processes).
logging: Used to log alerts to a file. The log file is named system_health.log.
datetime: Adds timestamps to log entries.
Thresholds:

The script checks for CPU, memory, and disk usage, as well as the number of running processes. Thresholds can be adjusted to your needs (e.g., CPU_THRESHOLD, MEMORY_THRESHOLD, DISK_THRESHOLD, MAX_PROCESSES).
Health Check Functions:

check_cpu(): Logs an alert if CPU usage exceeds the threshold.
check_memory(): Logs an alert if memory usage exceeds the threshold.
check_disk(): Logs an alert if disk space usage exceeds the threshold.
check_processes(): Logs an alert if the number of running processes exceeds the threshold.
Main Loop:

The script runs the monitoring every 60 seconds (time.sleep(60)). You can adjust the interval to suit your needs.
Output:

Alerts are printed to the console and logged to the system_health.log file.

How to Run:

Install psutil by running:
bash
Copy code
pip install psutil
Run the script:
bash
Copy code
python system_health_monitor.py


PROBLEM STATEMENT 2:-

Log File Analyzer:
Create a script that analyzes web server logs (e.g., Apache, Nginx) for
common patterns such as the number of 404 errors, the most requested
pages, or IP addresses with the most requests. The script should output a
summarized report using python

Here's a Python script that analyzes web server logs (e.g., Apache or Nginx) and provides a summary report for the following patterns:

The number of 404 errors (Not Found).
The most requested pages.
The IP addresses with the most requests.

This script assumes the log is in a common format, such as the Apache combined log format or Nginx's default log format:
127.0.0.1 - frank [10/Oct/2024:13:55:36 -0700] "GET /index.html HTTP/1.0" 200 2326

Log File Analyzer Script (Python)

import re
from collections import Counter

# Function to parse a log line and extract key fields
def parse_log_line(line):
    # Regular expression to match the common log format or combined log format
    log_pattern = re.compile(
        r'(?P<ip>\d+\.\d+\.\d+\.\d+)'        # IP address
        r' - - '                             # Separator
        r'\[(?P<datetime>.*?)\] '            # Date-time
        r'"(?P<method>\S+) (?P<url>\S+) \S+" '  # HTTP method and URL
        r'(?P<status>\d{3}) '                # Status code
        r'(?P<size>\d+|-)'                   # Response size
    )
    match = log_pattern.match(line)
    if match:
        return match.groupdict()
    return None

# Function to analyze the log file
def analyze_log(file_path):
    with open(file_path, 'r') as file:
        log_data = file.readlines()

    # Variables to store the counts and results
    total_requests = 0
    error_404_count = 0
    page_counter = Counter()
    ip_counter = Counter()

    # Process each log line
    for line in log_data:
        total_requests += 1
        log_entry = parse_log_line(line)

        if log_entry:
            # Track number of 404 errors
            if log_entry['status'] == '404':
                error_404_count += 1

            # Track requested pages
            page_counter[log_entry['url']] += 1

            # Track requests by IP address
            ip_counter[log_entry['ip']] += 1

    # Generate the report
    print("===== Log File Analysis Report =====")
    print(f"Total requests: {total_requests}")
    print(f"Number of 404 errors: {error_404_count}")
    print("\nTop 5 Most Requested Pages:")
    for page, count in page_counter.most_common(5):
        print(f"{page}: {count} requests")

    print("\nTop 5 IP Addresses by Number of Requests:")
    for ip, count in ip_counter.most_common(5):
        print(f"{ip}: {count} requests")

# Main function to run the analyzer
if __name__ == "__main__":
    log_file_path = "access.log"  # Path to your log file
    analyze_log(log_file_path)

Explanation:
Regular Expression (parse_log_line):

This function uses a regex pattern to match log entries. It extracts fields like the IP address, URL, HTTP status code, and response size from each log entry.
You can modify the regex if your log format deviates from the standard Apache or Nginx format.
Analyzing the Log:

The analyze_log function reads the log file, processes each line, and collects data on the total number of requests, 404 errors, the most requested pages, and the IPs with the most requests.
It uses Python’s collections.Counter to count occurrences efficiently.
Report:

The script outputs a summary report to the console that includes:
Total number of requests.
Number of 404 errors.
The top 5 most requested pages.
The top 5 IP addresses with the most requests.
How to Run:
Place your log file (e.g., access.log) in the same directory as the script or specify the full path to the log file.
Run the script:
bash
Copy code
python log_file_analyzer.py
Sample Output:
yaml
Copy code

sample out put:-

===== Log File Analysis Report =====
Total requests: 5000
Number of 404 errors: 35

Top 5 Most Requested Pages:
/index.html: 1200 requests
/contact.html: 300 requests
/about.html: 250 requests
/services.html: 200 requests
/home.html: 150 requests

Top 5 IP Addresses by Number of Requests:
192.168.1.1: 300 requests
192.168.1.2: 280 requests
192.168.1.3: 200 requests
192.168.1.4: 150 requests
192.168.1.5: 100 requests


Additional Enhancements:
Add more log metrics: Track other status codes (e.g., 500 errors), request methods (GET, POST), etc.
Custom thresholds: Add filters to flag specific patterns (e.g., more than 100 requests from a single IP in a short time).







PROBLEM STATEMENT 3:-

Automated Backup Solution:

Write a script to automate the backup of a specified directory to a remote
server or a cloud storage solution. The script should provide a report on the
success or failure of the backup operation. using python

Below is a Python script to automate the backup of a specified directory to a remote server using rsync or to a cloud storage solution like Amazon S3. The script also generates a report on the success or failure of the backup operation.

Automated Backup Script (Python)
This example uses both rsync for backup to a remote server via SSH and boto3 for backup to Amazon S3. You can configure it to use either method by adjusting the script configuration.

Pre-requisites:
For rsync to a remote server, ensure you have SSH access to the server.
For Amazon S3, install the boto3 package and configure AWS credentials using the AWS CLI or by setting environment variables (AWS_ACCESS_KEY_ID and AWS_SECRET_ACCESS_KEY).
Install boto3 for S3 integration:

bash
Copy code
pip install boto3
Backup Configuration:

Remote Server (via rsync): You’ll need the remote server address and SSH credentials.
Amazon S3: You’ll need an S3 bucket and AWS credentials.
Backup Script:
python
Copy code
import os
import subprocess
import boto3
from datetime import datetime
import logging

# Configure logging
logging.basicConfig(
    filename="backup_report.log",
    level=logging.INFO,
    format="%(asctime)s - %(levelname)s - %(message)s"
)

# Configuration for backup
BACKUP_SOURCE = "/path/to/local/directory"
REMOTE_SERVER = "user@remote-server:/path/to/backup/directory"  # For rsync to remote server
S3_BUCKET_NAME = "your-s3-bucket-name"  # For S3 backup
BACKUP_METHOD = "rsync"  # Options: 'rsync' or 's3'

# Function to log and print alerts
def log_alert(message, level='info'):
    if level == 'error':
        logging.error(message)
        print(f"ERROR: {message}")
    else:
        logging.info(message)
        print(message)

# Backup using rsync to a remote server
def backup_rsync():
    try:
        command = [
            "rsync", "-avz", "--delete", BACKUP_SOURCE, REMOTE_SERVER
        ]
        result = subprocess.run(command, stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)
        if result.returncode == 0:
            log_alert(f"Backup to remote server {REMOTE_SERVER} completed successfully.")
        else:
            log_alert(f"Backup failed with error: {result.stderr}", level='error')
    except Exception as e:
        log_alert(f"Backup to remote server failed with exception: {e}", level='error')

# Backup to Amazon S3
def backup_to_s3():
    try:
        s3_client = boto3.client('s3')
        for root, dirs, files in os.walk(BACKUP_SOURCE):
            for file_name in files:
                file_path = os.path.join(root, file_name)
                s3_key = os.path.relpath(file_path, BACKUP_SOURCE)  # Relative path for S3 key
                try:
                    s3_client.upload_file(file_path, S3_BUCKET_NAME, s3_key)
                    log_alert(f"Uploaded {file_name} to s3://{S3_BUCKET_NAME}/{s3_key}")
                except Exception as e:
                    log_alert(f"Failed to upload {file_name} to S3: {e}", level='error')

        log_alert(f"Backup to S3 bucket {S3_BUCKET_NAME} completed successfully.")
    except Exception as e:
        log_alert(f"Backup to S3 failed with exception: {e}", level='error')

# Main function to perform the backup
def perform_backup():
    log_alert(f"Starting backup from {BACKUP_SOURCE} using {BACKUP_METHOD}")
    
    # Perform the backup based on the chosen method
    if BACKUP_METHOD == "rsync":
        backup_rsync()
    elif BACKUP_METHOD == "s3":
        backup_to_s3()
    else:
        log_alert("Invalid backup method specified.", level='error')

    log_alert("Backup operation completed.")

# Run the backup script
if __name__ == "__main__":
    perform_backup()
Script Explanation:
Logging Configuration:

All actions and errors are logged to backup_report.log, which is stored in the same directory as the script.
Backup Methods:

rsync Backup:
Uses the rsync command to transfer files from the local directory to a remote server over SSH.
The --delete flag ensures that files deleted locally are also deleted on the remote server to maintain synchronization.
If rsync fails, the script logs an error with the returned message.

S3 Backup:

Uses the boto3 Python library to upload each file from the source directory to the specified S3 bucket.
The os.walk() function is used to traverse the directory and upload each file individually.
Logs the success of each file upload and any errors that occur during the process.

Backup Method Selection:

The BACKUP_METHOD variable determines whether the script will use rsync or S3 as the backup method.
The script supports either remote server backup using rsync or cloud storage backup using Amazon S3.
Report:

A log report is generated for each backup, providing information on the success or failure of each operation.
The logs include timestamps for each action, making it easy to track backup history.
How to Use:
Rsync to a Remote Server:

Set BACKUP_METHOD = "rsync".
Specify the local directory (BACKUP_SOURCE) and remote server destination (REMOTE_SERVER).
Make sure you have SSH access to the remote server, either by setting up passwordless SSH or providing credentials.
Backup to Amazon S3:

Set BACKUP_METHOD = "s3".
Specify the local directory (BACKUP_SOURCE) and your S3 bucket name (S3_BUCKET_NAME).
Ensure AWS credentials are configured on your system by using the AWS CLI (aws configure) or by setting environment variables for AWS access.
Run the Script:

To run the backup script manually, use the command:
bash
Copy code
python backup_script.py
Sample Log Output:
sql
Copy code
OUTPUT:-

2024-10-03 14:15:12,372 - INFO - Starting backup from /path/to/local/directory using rsync
2024-10-03 14:15:20,652 - INFO - Backup to remote server user@remote-server:/path/to/backup/directory completed successfully.
2024-10-03 14:15:20,653 - INFO - Backup operation completed.






