VPC Setup

Pre-requisites 

An private IP was assigned to me for this task as `10.0.9.0/24` so anywhere you see this, replace it with your private IP.

- For consistency we'll also be using, 

`10.0.0.0/16` as a reference to the public subnet.

`10.0.9.0/24` as a reference to the private subnet. Later changed to `10.0.91.0/24` to make the private subnet work.

We did not use a bastion subnet in this iteration.

Diagram

Creation of VPC

We have used "public" and "private" as a name for the prefixes, however you may use alternative names, but for easier tracking we use these names which is 
recommended. 

Public Subnet

- AWS search for VPC and follow the steps as below.

Step 1 

Create VPC

<img width="917" alt="Step 1 Create VPC" src="https://user-images.githubusercontent.com/126012715/234417913-6b2a92a0-66a8-4bf2-8ce2-512c2e897de2.png">

Step 2 

Configure the VPC settings, name and set IPv4 CIDR as the public subnet. Leaving everything else as default.

<img width="838" alt="Step 2 Config VPC" src="https://user-images.githubusercontent.com/126012715/234418870-dfab010a-0e19-4b6d-9aca-ee501d0f2829.png">

Step 3

Create Internet Gateaway

Name tag as the same name as above or similar.

<img width="865" alt="step 3 create IG" src="https://user-images.githubusercontent.com/126012715/234419048-62adf451-bdf6-4592-99a0-cbd2dee8531d.png">

Step 4 

To attach the Internet gateaway to the VPC. Action > connect > Personal VCP > Attach

<img width="893" alt="Step 4 Attach to VPC" src="https://user-images.githubusercontent.com/126012715/234419262-6346f086-4d95-4f78-8818-abb2e4e49986.png">

Step 5 

Create a public subnet. > Personal VPC > Edit name > No AZ preffence.

<img width="861" alt="Step 5 create subnet 1" src="https://user-images.githubusercontent.com/126012715/234420394-3b2d7342-2ed7-4142-9abe-bdbcf1d0d798.png">

- Select IPv4 CIDR as your private IP so `10.0.9.0/24` was inputted. 

Step 6

Create the route table. > Name edit > Select personal VPC > create

<img width="866" alt="step 6 Create Route table" src="https://user-images.githubusercontent.com/126012715/234423203-f61228c6-a4ce-495f-929e-6ed194a693df.png">

Step 7 

Edit our subnet associations. > Edit > Select subnet > Click routes > Click edit route.

<img width="1409" alt="Step 7 subnet Associations" src="https://user-images.githubusercontent.com/126012715/234424204-9d8f3f6d-58b7-4458-bc12-5ec92f28211f.png">

Final Step 

Edit our routes as below. 

<img width="1412" alt="Step 8 Edit routes" src="https://user-images.githubusercontent.com/126012715/234424281-f2bcf290-2738-4f0b-abbc-4d432af47ba1.png">

So the IGW is the internet gateaway. 

Creating App and DB Instances

Creating 2 ACLS

Step 1 

Create an AMI for App and DB from your running instances so we can stop or terminate the instances and our AMI would be there to cover it.

Please follow this link which shows you how to create an AMI but a brief description is as below. 

App Instance 

- Choose running instance.
- Action , Click on image. 
- Choose a relevant name, e.g Name-tech221-app-ami then create.

Instance from App AMI

- Select AMI for app we just created
- Launch Instance, Assign Name e.g name-tech221-app
- Shows your current AMI as using
- Keypair e.g tech221
- Network settings - Personal VPC > Public subnet
- Auto-Assign IP as enabled 

After making our security group we can

- Launch Instance and `.ssh` then connect the instance using `ubuntu` instead of `root` into the git bash terminal `cd` into `app` and use `npm start`.

Instance from DB AMI

- Select AMI for db we just created.
- Launch Instance , Assign Name e.g name-tech221-db
- Shows the current DB AMI as using
- Keypair e.g tech221
- Network settings - Personal VPC > private subnet
- Auto-Assign IP as Disable

As it is private, we won't be able to .ssh into it. Thus it won't work. 

Create 2 Security groups

- 1. App

<img width="785" alt="app inbound and outbound SG" src="https://user-images.githubusercontent.com/126012715/234433222-8bcf7b45-ded2-4b53-8d13-093654924faf.png">

- 2. db

<img width="1334" alt="db SG" src="https://user-images.githubusercontent.com/126012715/234433609-2291899e-04c6-4df4-aa3e-7a9a93f3bd40.png">

Create 2 Subnets in no pref AZ.

1. Public: `10.0.0.0/16`
- Assign Route table

(Public Subnet was done above when creating our VPC, respectively)

Private subnet for db instance

2. Private: `10.0.91.0/24`
- Assign to VPC and public ACL

- 1. Go on VPC and click create subnet
- 2. Select personal VPC
- 3. Choose CIDR: `10.0.91.0/24` , Public(`10.0.0.0/16`)
- 4. Create Route Table, Assign-Name
- 5. Asscociate with VPC, then Creat
- 6. Subnet Asssociation and edit
- 7. Choose your private subnet and create

SSH into the db from the app

Step 1 

- Go to Gitbash terminal 
