# Cloud gaming on EC2 Instance
This is the procedure of setting up Steam Gaming environment on EC2 instance for me.<br>
Entire architechture is below.

![game_on_AWS](https://user-images.githubusercontent.com/18078024/116800538-00d13f00-ab3d-11eb-8f60-6cd2e539ee3e.png)

## IAM Role
Create IAM Role for EC2 Instance to download NVDIA driver from S3 bucket. So you access [IAM console](https://console.aws.amazon.com/iam/home) to create IAM Role which name is like `Game`.
`AmazonS3ReadOnlyAccess` which is managed IAM policy is enough to download drivers from S3 bucket so you have to attach that policy to `Game` IAM Role.

## Security Group
Create Security Group with following parameters.
### Inbound rules
|Type|Protocol|Port range|Source|Description - optional|
|-----|-----|-----|-----|-----|
|Custom TCP|TCP|8000 - 8010|Your Global IP/32|Parsec|
|RDP|TCP|3389|Your Global IP/32|RDP|

### Outbound rules
|Type|Protocol|Port range|Destination|Description - optional|
|-----|-----|-----|-----|-----|
|All traffic|All|All|0.0.0.0/0|-|

## EC2 Instance
Create EC2 Instance with following parameters. Of course, you have to buy that as a spot instance.<br>
If you buy EC2 Instance as a Spot Instance, your pay is discounted.
**Spot Instance Price(2021/5/2 Tokyo Region)**
|Instance Type|Spot Price|On demand Price|CPU|Memory|
|---|---|---|---|---|
|g4dn.xlarge|$0.2130|$0.71|4|16|
|g4dn.2xlarge|$0.3045|1.015|8|32|

### EC2 Instance Parameter(Not all parameter)

|Parameter|Value|
|---|---|
|AMI|Microsoft Windows Server 2019 Base|
|Instance Type|g4dn.xlarge|
|Storage1|30GiB gp3|
|Storage2|100GiB gp3|
|IAM Role|(You create above procedure.)|
|Security Group|(You create above procedurs.)|

## Installation
You login to Windows EC2 Instance you create above. Official procedure is [here](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/connecting_to_windows_instance.html). And you do the procedure below to install the driver and set the configurations.
### 1. Change Administrator Password
Default password AWS generate is too complicated so I recommend to change.
```cmd
net user Administrator <Password>
```
### 2. Disable IE Enhance Security Configuration and install Chrome
Open Server Manager, disable IE Enhance Security Configuration to make easier to install Chrome. After that you install Chrome from https://www.google.co.jp/chrome .

![Screenshot from 2021-05-02 08-02-27](https://user-images.githubusercontent.com/18078024/116799489-bd72d280-ab34-11eb-8f77-515ab67b474a.png)

### 3. Install Parsec
Download Parsec installer from [here](https://parsecgaming.com/downloads). Just double click `parsec-windows.exe` to start the installation. After that, if you don't have the account for Parsec, you need to create. 

![Screenshot from 2021-05-02 08-16-32](https://user-images.githubusercontent.com/18078024/116799496-ccf21b80-ab34-11eb-86ae-f360050a1df5.png)

### 4. Configure sound
By and large, the remote server can't share the sound with the client. So you need to install [Virtual Audio Cable](https://en.wikipedia.org/wiki/Virtual_Audio_Cable). I used VB-Audio. You click [this link](https://vb-audio.com/Cable/) to download zip file which contain the installer. Unzip it, execute `VBCABLE_Setup_x64.exe` to install that then you need to reboot OS.<br>
After that, you visit [Control Panel] > [Hardware] > [Sound] to confirm the installation of the sound driver was completed.

### 5. Drive initialization
This EC2 instance provisioned the secondary virtual disk drive so you need to initialize that disk drive as a drive D:\\. The downloaded games are placeed on the drive.Offical procedure is [here](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/ebs-using-volumes.html).

### 6. Install Steam
Create the folder for placing downloaded game like `D:\steam`. Then, you access [Steam download page](https://store.steampowered.com/about/) to install Steam. After that, you visit [View] > [Settings] > [Downloads] > [Content Libraries] to chage the default download folder.

![Screenshot from 2021-05-02 09-33-38](https://user-images.githubusercontent.com/18078024/116799503-dda29180-ab34-11eb-8ee9-6e4d4714dd4b.png)

### 7. Install NVIDIA GRID driver
Go to [this page](https://docs.aws.amazon.com/AWSEC2/latest/WindowsGuide/install-nvidia-driver.html#nvidia-GRID-driver) and following the instruction to install NVIDIA GRID driver. What I did in the installation is following.
- I didn't do Option in the instruction to install NVIDIA driver.
- I choose Express install option in the intallation.

### 8. Setting graphic device
You visit [Device Manager] > [Display adapters]. Then you disable `Microsoft Basic Display Adapter`.

<br><br>Installation has been done.

## Connect to Server through Parsec
You need to install Parsec on your client computer from [here](https://parsecgaming.com/downloads). Then you connect to Server through Parsec like below.

![Screenshot from 2021-05-02 10-43-23](https://user-images.githubusercontent.com/18078024/116799509-ec894400-ab34-11eb-95d1-820d2fd04e91.png)

## Other configuration
- You need to choose `Software` on Decoder.

![Screenshot from 2021-05-02 10-49-42](https://user-images.githubusercontent.com/18078024/116799553-3b36de00-ab35-11eb-8745-71dd34483ed7.png)

- Resolution is 1920 × 1080.
