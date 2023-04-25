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




