2 - tier architecture deployment in AWS 

Diagram 


Delpoying our steps and goals:

- Copying our provisions codes/app to the AWS ec2 instances
- Installing our node Dependencies for the node app (NGINX)
- NPM install or NPM start
- Node app with the public port 3000 should therefore pop the NGINX configuration

Step 1

Make a new instance in AWS with the same ubuntu 20.4 or 18.4 for less hassle in the future (used the third down from the "Browse AMI")
 
- Making an App instance.

- Connect the instance to the local host using the right security group, ssh and http groups. 

Security Group should be as follows:

- Select the instance and go to SG

- "Edit inbound rules"

- Add new rule: Custom TCP, Set Port 3000, Source "IPv4 from anywhere (0.0.0.0)", Save rule.

- 1. From your admin git bash terminal use the `.ssh` command to get into the tech221.pem file by the .ssh folder.

- Then use `chmod 400 tech221.pem` and `ssh -i "tech221.pem" ubuntu@ec2-52-51-76-210.eu-west-1.compute.amazonaws.com`

- 2. Open another terminal and get into the `.ssh` folder from admin / local host.

Step 2

Connecting the app and copying our app folder from our `.ssh` into our AWS ubuntu instance folder.

- To get pathway use `cd` then `pwd` to return to directory pathway (or use VScode to get pathway from the folder).

- `scp` command into the `.ssh` folder in the git bash local host terminal not the ec2 ubuntu app terminal.

Hint: use the SCP command (secure copy)

`scp -i example.pem -r app-folder example@example.com:~/`

`scp -i "<pvt-key>" -r <copy-pathway> ubuntu@ec2-<ip-address>.eu-west-1.compute.amazonaws.com:<destination>`

- 1. The `-i` refers to the private key file to use for authentication
- 2. The `-r` refers to the app folder pathway which should be copied recursively ( App folder Path copied from vscode )
- 3. The `example@example.com:` specifies the remote host / destination directory for the copied folder.
- 4. Ubuntu@ec2 (with ip address), uses the ssh login and the ec2 public dns
- 5. :home/ubuntu, specifies where the copied file will go on the remote server

Final SCP - 

`scp -i ~/.ssh/tech221.pem -r /Users/Admin/Documents/Virtualisation/app ubuntu@ec2-52-51-76-210.eu-west-1.compute.amazonaws.com:/home/ubuntu/`

Step 3

- Install Required Dependencies for node app in your AWS App git bash terminal.

`sudo apt update -y` , `sudo apt upgrade -y` , `sudo apt install nginx` and follow the commands as illustrated as below, HOWEVER, since we had our provsion.sh file within our app folder, we provisioned it as below to execute the commands rather than manually doing so. 

- `cd` into the app folder then `cat provsion.sh` , you may edit if necessary. 
- Run our provsion.sh file `sudo ./provsion.sh`.

NPM start 
- Run `node app.js` to start app

Final Step

App is now available on port 3000

- Copy the App Instance IPv4 address and paste in the browser
- Add `:3000` at the end like so 

Setting up the app instance: 

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

Setting up the reverse proxy

Step 1

- `Cd` to the below folder to navigate inside the nginx configuration folder

`/etc/nginx/sites-available`

Then: `sudo rm -rf default`

- To delete the current default file which you will replace OR you may create a new configuration file.

We used `sudo nano default` to create a new conf file thus deleting our existing one.

Then you'll need to create a new default file with the following properties:

`server_IP` EC2 IP address

  server {
     listen 80;
     server_name <server_IP>;

     location / {
         proxy_pass http://<server_IP>:3000;
         proxy_set_header Host $host;
         proxy_set_header X-Real-IP $remote_addr;
         proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
         proxy_set_header X-Forwarded-Proto $scheme;
   }
}

- `Cntrl+x` to exit and `y` to save and `enter` to save the name and exit

Step 2

- Enabling through a symbolic link to enable a new config file.
- Our `default` is our config file we just created as above.

`sudo ln -s /etc/nginx/sites-available/default /etc/nginx/sites-enabled/default`

Step 2.2

If `failed to create symbolic link, file exists` we can unlink the file and link it again.

`sudo unlink /etc/nginx/sites-enabled/default`

Set the database location as an env variable:

`sudo nginx -t`

`cd /home/vagrant/`

`export DB_HOST=mongodb://[ip of db instance]:27017/posts`