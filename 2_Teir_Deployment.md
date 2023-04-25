<h1>2 - tier architecture deployment in AWS</h1>

![2 teir diagram](https://user-images.githubusercontent.com/126012715/234212038-a9c4bc69-ed99-4b03-951c-90cc33f101d2.JPG)

<h2>Delpoying our steps and goals:</h2>

- Copying our provisions codes/app to the AWS ec2 instances
- Installing our node Dependencies for the node app (NGINX)
- NPM install or NPM start
- Node app with the public port 3000 should therefore pop the NGINX configuration

<h4>Step 1</h4>

Make a new instance in AWS with the same ubuntu 20.4 or 18.4 for less hassle in the future (used the third down from the "Browse AMI" then "community AMI")
 
Making an App instance.

- Connect the instance to the local host using the right security group, ssh and http groups. 

Security Group should be as follows:

- Select the instance and go to SG

- "Edit inbound rules"

- Add new rule: Custom TCP, Set Port 3000, Source "IPv4 from anywhere (0.0.0.0)", Save rule.

- 1. From your admin git bash terminal use the `.ssh` command to get into the tech221.pem file by the .ssh folder.

- Then use `chmod 400 tech221.pem` and `ssh -i "tech221.pem" ubuntu@ec2-52-51-76-210.eu-west-1.compute.amazonaws.com`

- 2. Open another terminal and get into the `.ssh` folder from admin / local host.

<h4>Step 2</h4>

Connecting the app and copying our app folder from our `.ssh` into our AWS ubuntu instance folder.

- To get pathway use `cd` then `pwd` to return to directory pathway (or use VScode to get pathway from the folder).

- `scp` command into the `.ssh` folder in the git bash local host terminal not the ec2 ubuntu app terminal.

Hint: use the SCP command (secure copy)

`scp -i example.pem -r app-folder example@example.com:~/`

`scp -i "<pvt-key>" -r <copy-pathway> ubuntu@ec2-<ip-address>.eu-west-1.compute.amazonaws.com:<destination>`

- 1. The `-i` refers to the private key file to use for authentication
- 2. The `-r` refers to the app folder pathway which should be copied recursively ( App folder Path copied from vscode )
- 3. The `example@example.com:` specifies the remote host / destination directory for the copied folder.
- 4. `Ubuntu@ec2 (with ip address)`, uses the ssh login and the ec2 public dns
- 5. `:home/ubuntu`, specifies where the copied file will go on the remote server

<h4>Final SCP -</h4>

`scp -i ~/.ssh/tech221.pem -r /Users/Admin/Documents/Virtualisation/app ubuntu@ec2-52-51-76-210.eu-west-1.compute.amazonaws.com:/home/ubuntu/`

<img width="583" alt="scp command line" src="https://user-images.githubusercontent.com/126012715/234126956-c359ee42-29ea-4b57-a501-fc427597ed3e.png">

<h4>Step 3</h4>

- Install Required Dependencies for node app in your AWS App git bash terminal.

`sudo apt update -y` , `sudo apt upgrade -y` , `sudo apt install nginx` and follow the commands as illustrated as below, HOWEVER, since we had our provsion.sh file within our app folder, we provisioned it as below to execute the commands rather than manually doing so. 

- `cd` into the app folder then `cat provsion.sh` , you may edit if necessary. 
- Run our provsion.sh file `sudo ./provsion.sh`.

<img width="588" alt="cat provsion 3000 port" src="https://user-images.githubusercontent.com/126012715/234127139-46358cac-cdad-4bed-a11f-ffd900256463.png">

NPM start 
- Run `node app.js` to start app

<h4>Final Step</h4>

App is now available on port 3000

- Copy the App Instance IPv4 address and paste in the browser
- Add `:3000` at the end like so:

<img width="1427" alt="52 51 76 210 3000 posts" src="https://user-images.githubusercontent.com/126012715/234129183-51f6e259-ee63-48cb-9efd-0fd0c39a9ffc.png">

<h3>Setting up the app instance</h3>

SSH in to the instance using the information given in the ec2 dashboard

Updating our VM: `sudo apt-get update -y`

Upgrading our VM: `sudo apt-get upgrade -y`

Installing nginx: `sudo apt-get install nginx -y`

Installing node.js:

`sudo apt install node.js -y`
`curl -sL https://deb.nodesource.com/setup_12.x | sudo -E bash -` , download a newer version of node.js
`sudo apt install nodejs -y`

Installs pm2 globally: `sudo npm install pm2 -y -g`

Checks status of nginx: `systemctl status nginx`

<h2>Setting up the reverse proxy</h2>

<h4>Step 1</h4>

- `Cd` to the below folder to navigate inside the nginx configuration folder

`/etc/nginx/sites-available`

Then: `sudo rm -rf default`

- To delete the current default file which you will replace OR you may create a new configuration file.

We used `sudo nano default` to create a new conf file thus deleting our existing one.

Then you'll need to create a new default file with the following properties:

`server_IP` EC2 IP address

      server {
         listen 80;`
         server_name <server_IP>;`

         location / {
             proxy_pass http://<server_IP>:3000;
             proxy_set_header Host $host;
             proxy_set_header X-Real-IP $remote_addr;
             proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
             proxy_set_header X-Forwarded-Proto $scheme;
       }
    }

- `Cntrl+x` to exit and `y` to save and `enter` to save the name and exit

<h4>Step 2</h4>

- Enabling through a symbolic link to enable a new config file.
- Our `default` is our config file we just created as above.

`sudo ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled/default`

<h4>Step 2.2</h4>

If `failed to create symbolic link, file exists` we can unlink the file and link it again.

`sudo unlink /etc/nginx/sites-enabled/default`

<h4>Step 3</h4>

- `sudo nginx -t` to check errors in the configuration
- `sudo systemctl reload nginx` , reload Nginx
- `sudo systemctl enable nginx`

Navigate using `cd` to home then `cd app` 

- `node app.js`, launch app in the background.
- Paste the AWS app instance IP address into browser, without the port, to check if works then add `/posts` at the end.

Debugging !

`sudo lsof -i :3000` to kill the process within the port

`sudo kill -9 PID`

Use the following commands as above if errors occur.

<img width="1434" alt="52 51 76 210 posts reverse proxy" src="https://user-images.githubusercontent.com/126012715/234129047-04cd68c5-04ef-4d0d-b3b6-bf806873419d.png">

<h2>Setting up the DB instance</h2>

Optional After-preparation once inside instance in terminal (If bind IP is also implemented as mongod.conf and to use the provisiondb.sh)

- To configure the files to make DB more accessible, we first move our virtualisation folder into our db instance as below.
- 1. Git clone our `Virtualisation` folder from `GitHub` to move our environment folder to our ubuntu home within our db instance. As below:

<img width="586" alt="Git Clone our Environment var folder" src="https://user-images.githubusercontent.com/126012715/234127504-d54f28c7-325e-4985-a393-bdc0e47d20b8.png">

<h4>Step 1</h4>

- Create a new AWS db instance.
- 1. OS ubuntu 18.04 (20.04 does not allow mongodb to be installed)

<h4>Step 2</h4>

- Editing our Security group
- Add an SSH key connection with your "IP"
- Add Custom TCP connection with the port being `27017` (Mongodb) and IP as `anywhere, 0.0.0.0`
- Add Custom TCP connection with the port being 3000 (Sparta App) and IP as `anywhere, 0.0.0.0`

<h4>Step 3</h4>

- Launch Instance
- Connect instance using SSH connection and Git Bash terminal

<h4>Step 4</h4>

Use the following commands to install MongoDB within the db instance.

`sudo apt update -y` 

`sudo apt upgrade -y`

`sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv D68FA50FEA312927`

`echo "deb https://repo.mongodb.org/apt/ubuntu xenial/mongodb-org/3.2 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.2.list`

`sudo apt update -y` 

`sudo apt upgrade -y`

`sudo apt-get install -y mongodb-org=3.2.20 mongodb-org-server=3.2.20 mongodb-org-shell=3.2.20 mongodb-org-mongos=3.2.20 mongodb-org-tools=3.2.20

`sudo systemctl start mongod`

`sudo systemctl status mongod`

<h2>DB Machine and App Machine Connection</h2>

<h3>DB instance</h3>
 
- 1. `cd /etc/`
- 2. `sudo nano mongod.conf` , Scroll down and configure `bindip` to `0.0.0.0`
- 3. Save (cntrl+x , y) and exit file (Enter)

<img width="580" alt="ip" src="https://user-images.githubusercontent.com/126012715/234127634-ab9e839f-fbda-4e7b-bfa8-0730df662388.png">

- 4. `sudo systemctl restart mongod`, restart mongod
- 5. `sudo systemctl enable mongod`, enable mongod
- 6. `sudo systemctl status mongod`, Check if running

<h3>App instance</h3>

- 1. `cd` back to home folder
- 2. `sudo nano .bashrc` , Create environment variable within the .bashrc file

`export DB_HOST=mongodb://[ip of db instance]:27017/posts`

- Connect to mongodb database at the specific IP address (e.g db instance) that is assigned and the through to the posts.
Set the database location as an env variable:

- 3. `source .bashrc` , updates and applies to the changes within .bashrc file
- 4. `printenv DB_HOST` e.g `mongodb://54.78.135.221:27017/posts` to check the changes to the variable.
- 5. `cd app`
- 6. `node seeds/seed.js` - seed data to database
- 7. `node app.js`

<h4>Final Step</h4>

- Copy and paste the App Instance IP address to check if the post page works along with mongodb

<img width="1434" alt="52 51 76 210 posts reverse proxy" src="https://user-images.githubusercontent.com/126012715/234131494-cbb738c5-b8b2-4e7b-b3fe-0159f9f54232.png">

<h3>App Instance Security Group</h3>

Setting up security permissions.

<img width="835" alt="App sg" src="https://user-images.githubusercontent.com/126012715/234132985-f02be7df-4b7e-4e17-94eb-f2cc733f8a6a.png">

<h3>DB instance Security Group</h3>

<img width="836" alt="db SG" src="https://user-images.githubusercontent.com/126012715/234133009-986a5726-6830-4f2a-a1ea-301f4c25b34b.png">

 
