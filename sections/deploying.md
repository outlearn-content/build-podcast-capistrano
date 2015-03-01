### 2\. deploy a simple `html` file with Github and AWS EC2

#### 1\. Open a new Github Repo

  1. Open a new github Repo

#### 2\. Prepare a local project with git

  1. Go to an empty folder for the project
  2. Create a simple `index.html`
  3. Initiate a git repository and push to github

        git init
    git add README.md
    git commit -m "first commit"
    git remote add origin git@[username]/[repo-name].git
    git push -u origin master


#### 3\. Prepare a capistrano task

  1. Go to the project folder and one level up in the command line (or anywhere you wish) to create the Capistrano task
  2. run in the command line `capify .` and this will create:

        .
    |-- Capfile
    `-- config
        `-- deploy.rb


  3. Include the [capistrano config](https://help.github.com/articles/deploying-with-capistrano) in file `deploy/config.rb`

        set :application, "cap-github-ec2"
    set :deploy_to, "/var/www/html"

    set :scm, :git
    set :repository, "[[email protected]](/cdn-cgi/l/email-protection):chinee/hello-cap.git"
    set :scm_username, "sayanee"

    set :location, "ec2-54-234-17-185.compute-1.amazonaws.com"
    role :app, location
    role :web, location
    role :db,  location, :primary => true
    set :user, "ec2-user"
    ssh_options[:keys] = ["/Users/sayanee/.ssh/ec2.pem"]
    default_run_options[:pty] = true

    set :branch, 'master'
    set :scm_verbose, true


#### 4\. Create an EC2 instance

  1. Create a simple [ec2 instance](https://github.com/sayanee/Build-Podcast/tree/master/033-aws#1-elastic-cloud-compute-ec2-for-a-simple-indexhtml)

  2. SSH login into EC2 instance with the `*.pem` file which has the correct permissions

        chmod 600 mykey.pem
    ssh -i mykey.pem ec2-user@[public-dns]


  3. Install Git

        sudo yum install git


  4. Install Apache Server

        sudo yum install httpd


  5. Ensure that the default folder exisrs

        cd ../../var/www/html/


  6. Start the Apache server and check its status

        sudo service httpd start
    sudo service httpd status


  7. visit the _ec2 public dns_ with the addresshttp://ec2-xx-xxx-xx-xxx.compute-1.amazonaws.com and it should show a default Amazon Linux AMI Test Page

  8. Create an SSH key pair and upload to github

        cd ~/.ssh
    ssh-keygen -t rsa -f [name] -C "[email]"


  9. Create the SSH Config file

        touch ~/.ssh/config


  10. Edit the `~/.ssh/config` using an editor such as `sudo nano ~/.ssh/config` file with the following contents:


       Hostname github.com
       IdentityFile ~/.ssh/[name]


  1. Change permsissions for the config file

        chmod 600 ~/.ssh/config


  2. copy the content of the public key:

        cat ~/.ssh/name.pub


  3. Go back to the Github repo page and add SSH keys to `Github Repo > Setting > Deploy Keys` and paste in the contents of `~/.ssh/name.pub`

  4. Test whether SSH into Github works

        ssh -T [[email protected]](/cdn-cgi/l/email-protection)


  5. The project will be deployed to `/var/www/html` so ensure the permission is set to the current user

        cd /var/www/html
    sudo chown -R ec2-user:ec2-user .


  6. By default capistrano will release the latest project into the folder `/var/www/[project-name]/current/public`, so we will need to tell the Apache server to point to it. Open the config file

        sudo nano /etc/httpd/conf/httpd.conf


  7. Edit the lines to point to it

        …
    DocumentRoot "/var/www/html/current"
    …
    <Directory "/var/www/html/current/public">


  8. Stop and restart the Apache server

        sudo service httpd stop
    sudo service httpd start


#### 5\. Deploy using Capistrano and Github

  1. Come back to the Local machine in the folder where the capistrano task is defined. Check everything is working!

        cap deploy:setup


  2. Deploy from Github to EC2

        cap deploy


  3. View the public DNS in the browser and it should show your simple `index.html` file!
