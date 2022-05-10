## DEVOPS TOOLING WEBSITE SOLUTION

Here we are being introduced to a single DevOps Tooling Solution that will consist of the following:

1. Jenkins – free and open source automation server used to build CI/CD pipelines.
 
2. Kubernetes – an open-source container-orchestration system for automating computer application deployment, scaling, and management.
3. Jfrog Artifactory – Universal Repository Manager supporting all major packaging formats, build tools and CI servers. Artifactory.

4. Rancher – an open source software platform that enables organizations to run and manage Docker and Kubernetes in production.

5. Grafana – a multi-platform open source analytics and interactive visualization web application.

6. Prometheus – An open-source monitoring system with a dimensional data model, flexible query language, efficient time series database and modern alerting approach.

7. Kibana – Kibana is a free and open user interface that lets you visualize your Elasticsearch data and navigate the Elastic Stack.

In this project I will implement a solution that consists of following components:

- Infrastructure: AWS
- Webserver Linux: Red Hat Enterprise Linux 8
- Database Server: Ubuntu 20.04 + MySQL
- Storage Server: Red Hat Enterprise Linux 8 + NFS Server
- Programming Language: PHP
- Code Repository: Github

## TASK

### Step 1 – Prepare NFS Server

Spin up a new EC2 instance with RHEL Linux 8 Operating System.

Based on LVM experience from Project 6, Configure LVM on the Server.

Instead of formating the disks as ext4 you will have to format them as xfs

Ensure there are 3 Logical Volumes. lv-opt lv-apps, and lv-logs

    sudo lvcreate -n lv-apps -L 9G vg-webdata
    sudo lvcreate -n lv-logs -L 9G vg-webdata
    sudo lvcreate -n lv-opt -L 9G vg-webdata

Create mount points on /mnt directory for the logical volumes as follow:
Mount lv-apps on /mnt/apps – To be used by webservers
Mount lv-logs on /mnt/logs – To be used by webserver logs
Mount lv-opt on /mnt/opt – To be used by Jenkins server in Project 8

Install NFS server, configure it to start on reboot and make sure it is u and running





