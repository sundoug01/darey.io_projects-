
## Tooling website deployment automation with CI 

- The architecture
  
![image](https://github.com/sundoug01/darey.io_projects-/assets/28840209/f43e78f2-6b50-4b46-8c7e-83f7521edc8a)



# Step 1 – Install Jenkins server
- Create an AWS EC2 server based on Ubuntu Server 20.04 LTS and name it “Jenkins-server”

  <img width="1138" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/78d0603c-57f8-40bf-a600-28840dd04ab7">


- Jenkins Debian Packages
[Jenkins installation](https://pkg.jenkins.io/debian/)

Make sure Jenkins is up and running
```
sudo systemctl status jenkins
```
By default Jenkins server uses TCP port 8080 – open it by creating a new Inbound Rule in your EC2 Security Group

![image](https://github.com/sundoug01/darey.io_projects-/assets/28840209/d72ee4a7-b43e-4706-942b-7ad3b4d53c4e)

Perform initial Jenkins setup.
From your browser access ``` http://<Jenkins-Server-Public-IP-Address-or-Public-DNS-Name>:8080 ```

You will be prompted to provide a default admin password

![image](https://github.com/sundoug01/darey.io_projects-/assets/28840209/dd4e48b0-19b5-4cc3-9b80-112eed0d4d3a)

![image](https://github.com/sundoug01/darey.io_projects-/assets/28840209/ffd9d3a5-0c1b-4927-8ae0-b1575726cee7)

![image](https://github.com/sundoug01/darey.io_projects-/assets/28840209/94f10c51-4151-46b1-b1f2-64c7f95e5c02)

# Step 2 – Configure Jenkins to retrieve source codes from GitHub using Webhooks

<img width="1171" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/bf86a00d-81d3-4fcd-86b7-096c146d667b">

- Go to Jenkins web console, click “New Item” and create a “Freestyle project”

  <img width="709" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/3d922783-04e2-409f-b7c9-a2d6a6045e4d">

But this build does not produce anything and it runs only when it is triggered manually. 

Click “Configure” your job/project and add these two configurations
- Configure triggering the job from GitHub webhook:

<img width="760" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/09a814a9-fcd6-4ddd-bd0d-c439b08eba65">

- Configure “Post-build Actions” to archive all the files – files resulted from a build are called “artifacts”.

<img width="597" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/f01df929-da98-40ba-a97e-59322038a15b">



Now, go ahead and make some change in any file in your GitHub repository (e.g. README.MD file) and push the changes to the master branch.

You will see that a new build has been launched automatically (by webhook) and you can see its results – artifacts, saved on Jenkins server

<img width="963" alt="image" src="https://github.com/sundoug01/darey.io_projects-/assets/28840209/33170ed4-6e79-46a0-a523-75404e197f15">

By default, the artifacts are stored on Jenkins server locally
```
ls /var/lib/jenkins/jobs/tooling_github/builds/<build_number>/archive/
```

