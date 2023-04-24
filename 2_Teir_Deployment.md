2 tier app deployment 

Diagram 

Step 1

Make a new instance in AWS with the same ubuntu 20.4.

- Connect the instance to the local host using the right security group, ssh and http groups. 

- 1. From your admin git bash terminal use the `.ssh` command to get into the tech221.pem file by the .ssh folder.

- 2. Open another terminal and get into the `.ssh` folder from admin.

Step 2

Connecting the app and copying our app folder from our `.ssh` into our AWS ubuntu instance app folder.
- To get pathway use `cd` then `pwd` to return to directory pathway.
- `scp` command into the `.ssh` folder in the git bash terminal not the ec2 ubuntu terminal.

Hint: use the SCP command (secure copy)

`scp -i example.pem -r app-folder example@example.com:~/`

`scp -i "<pvt-key>" -r <copy-pathway> ubuntu@ec2-<ip-address>.eu-west-1.compute.amazonaws.com:<destination>`

- 1. The `-i` refers to the private key file to use for authentication
- 2. The `-r` refers to the app folder pathway which should be copied recursively ( App folder Path copied from vscode )
- 3. The `example@example.com:` specifies the remote host / destination directory for the copied folder.
- 4. Ubuntu@ec2 (with ip address), uses the ssh login and the ec2 public dns
- 5. :home/ubuntu, specifies where the copied file will go on the remote server

Final SCP - `scp -i ~/.ssh/tech221.pem -r /Users/Admin/Documents/Virtualisation/app ubuntu@ec2-52-51-76-210.eu-west-1.compute.amazonaws.com:/home/ubuntu/`

SSH in to the instance using the information given in the ec2 dashboard

Updating our VM: `sudo apt-get update -y`

Upgrading our VM: `sudo apt-get upgrade -y`

Installing nginx: `sudo apt-get install nginx -y`

Installing node.js:

`sudo apt install node.js -y`

curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash -
sudo apt install nodejs -y

Installs pm2 globally: `sudo npm install pm2 -y -g`

Checks status of nginx: `systemctl status nginx`

Setting up the reverse proxy. Go to: `/etc/nginx/sites-available`

Then: `sudo rm -rf default`

Then you'll need to create a new default file with the following properties:

server {
    listen 80;

    server_name _;

    location / {
        proxy_pass http://192.168.10.100:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

Set the database location as an env variable:

`sudo nginx -t`

`cd /home/vagrant/`

`export DB_HOST=mongodb://[ip of db instance]:27017/posts`
