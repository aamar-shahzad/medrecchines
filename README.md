# MedRec
This project is a Demo that integrates the front and back ends and Fabric made by the author during the process of learning Fabric-medical information sharing based on blockchain.

There are inevitable awkwardness in the code. The deployment process, front-end and back-end interactions with the Fabric network have been tested. If there is a problem in the deployment or any part is not detailed enough, you can contact me by mentioning Issue , The following construction process only demonstrates the part about the blockchain, the others are not demonstrated (and not perfect, I will add it later)

> The chaining process in the project uses an asymmetric key RSA for encryption, the local IPFS is used to simulate file storage, and the blockchain uses Fabric.
>
> The SDK uses Node SDK; the Fabric version is 1.4.4; the chain code is written in Go, and the back end is the Express framework of Node.
>
> The first part is mainly about the pre-installation of the environment (all the required toolkits have been placed in the MedRec file by the author, no other downloads are required. As for some other Node and mysql environments, you can install it by yourself through online tutorials, as long as the version is correct Ok)
>
> The second part is the demonstration of the project
## One. ready

#### 1. Preliminary environmental preparation

(1) The system is **Ubuntu16.04** (I am a virtual machine built)

(2) Install **Mysql** on ubuntu, the version is **5.7.31**, just follow the online tutorial to install it.

(3) Find the **MedRecord.sql** file in the database directory in the MedRec directory (this is the file of the local database that needs to be used in the project). Import the file into the mysql database in Ubuntu. (The name of the **database I created here is MedRec**)

If you are building a virtual machine environment on Windows, you can use Navicat to remotely connect to the virtual machine as I did below, and import the above sql file into the Mysql database of the virtual machine

![image-20200909134016816](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200909134016816.png)

 The following is the table of the imported database **MedRec**:

![image-20200909134031322](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200909134031322.png)

 

After importing the table, check whether there are any records in the **hospital** and **user** tables, and delete if there are any! ! ! ! Must be deleted! !

After importing, there are two records in each of the two tables, just delete them

(3) Optional: Install the **nvm** version to 0.33.10 (the version is not limited, nvm is a tool used to manage the node version, you can easily install node, and easily switch between node and npm versions), of course It can also be installed without nvm, mainly for installing Node. You can choose your own way to install Node

(4) Use nvm to install the **Node.js** version as **v10.19.0** (npm and cnpm are both management tools for downloading node dependencies, similar to maven in java. Note that **npm must be configured domestically Mirror source, otherwise the download will be stuck for a long time**)

(5) Install the go language environment, the version is **>=1.11** and above, the version 1.11 is used here

 The above basic environment installation can be installed using the online tutorial, and I will not repeat it here.

---

(6) Install **go-ipfs** to build a local IPFS. Here, **go-ipfs\v0.4.23\linux-amd64.tar.gz** is used. This is slow to download on github, and it is in MedRecord Provided in the **IPFSInstall** folder of the directory

- Installation process:


1. Copy the compressed package in the file to the "**home**" directory in the virtual machine, for example, my one here is

   ![image-20200909135034037](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200909135034037.png)

2. Unzip:

   ```shell
   tar -xzvf go-ipfs_v0.4.23_linux-amd64.tar.gz
   ```

   At this time, you will see that the **go-ipfs** folder is generated in the same level directory

   ![image-20200909135230281](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200909135230281.png)

3. Configure the environment that uses IPFS globally:

   ```shell
   sudo mv go-ipfs/ipfs /usr/local/bin/ipfs
   ```

   If you run in the terminal at this time:

   ```ipfs
   ipfs
   ```

   If the following appears, the installation is successful

   ![image-20200909135535099](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200909135535099.png)

4. Initialize the local IPFS environment:

   ```shell
   ipfs init
   ```

   ![image-20200909140211031](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200909140211031.png)

   In this way, the initialization is successful, execute the above ipfs cat /ipfs/xxxxxxxxxxxxxxxxxxxx (this hash is different for everyone, just execute the one on your own machine)

   ```shell
   ipfs cat /ipfs/QmS4ustL54uo8FzR9455qaxZwuMiUhyvMcX9Ba8nUH4uVv/readme
   ```

   The following appears:

   ![image-20200909140548528](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200909140548528.png)

5. Start the local IPFS daemon (only by starting this can you use IPFS to upload files and access files on IPFS):

   ```shell
   ipfs daemon
   ```

   ![image-20200909140754676](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200909140754676.png)

   At this point, the IPFS environment has been set up, let’s try the simple file uploading of IPFS. Don’t close this process in the project, it’s always running in the background.

6. Try uploading a file, create a file **a.txt** in the same level directory, the content of the file is as follows:

   ![image-20200909141200628](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200909141200628.png)

7. Upload the file (execute in the a.txt directory):

   ```shell
   ipfs add a.txt
   ```

   ![image-20200909141351986](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200909141351986.png)

   You can see that there is a string of hash behind the added, this is the hash address that identifies the a.txt file on IPFS, through which you can pass the ipfs specific gateway (after the ipfs daemon is started, you can see the local gateway and the port is 127.0.0.1:8080 /ipfs) to get the a.txt file

   So you can access the file through the 127.0.0.1:8080/ipfs+hash address and open the browser:

   http://127.0.0.1:8080/ipfs/QmWcNW1SSeYzYzHhVXHaXrZaTnRVce1BEoffQqWUHsr4Fu

   ![image-20200909143225920](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200909143225920.png)

​ You can see the files we uploaded to IPFS.

> The local gateway is used here for access, and other IPFS gateways can also be used for access. Even if you can access the external network, you need to wait for a while to get the file. Here is for the convenience of testing and use. A local gateway is used. IPFS has a lot of alternate gateways that can be accessed. You can search for many on the Internet.
>
> At this point, the local node deployment of IPFS is over, and the subsequent case files are also uploaded and accessed in this way (only the IPFS API of a specific language is used for uploading, the bottom layer is still the same, all through this ipfs add method )



#### 2. Download Fabric1.4.4, and run the Fabric example successfully, you can ensure that all the above images, source code, dependencies, and tool versions are all correct. It is very important to complete this step before proceeding with the following operations!

 

#### 3. Copy the project MedRec to the virtual machine in the following directory:

 ![image-20200909143949808](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200909143949808.png)



Note: The directory where the **MedRec** folder is located must be placed correctly. It is best to have the same directories in the above picture, otherwise the path may be wrong, because some parts of the project are hard to write.



## 2. Start the MedRec project

#### 1. Two important operations need to be done before each start of the project

(1) Ensure that there are no records in the **hospital and user** tables of the database, and **delete** if there are any.

(2) Ensure that there are no files in the /home/username/go/src/github.com/hyperledger/MedRec/App/server/Identity/wallet directory, if any, **delete**!

(3) Start the local IPFS process, that is, run **ipfs daemon** to start it

(4) If it is the first time to copy the project into the virtual machine, you need to download related dependencies (you can also use cnpm to download the dependencies, of course, using npm to download dependencies will be slower, because of the wall, you need to download with **npm Equipped with Taobao mirror source download address**)

![image-20200911115415026](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200911115415026.png)

![image-20200911115528162](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200911115528162.png)

![image-20200911115632553](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200911115632553.png)

Download the corresponding dependencies in the above three directories.

#### 2. Start the Fabric network of the project

Enter the following directory:

![image-20200911115845729](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200911115845729.png)

Start the network, execute:

```shell
./start.sh up
```

It takes a while to start the network, depending on the configuration of your machine... ..... (Three channels and three chain codes are started during the startup process, but we only use one channel commonchannel, two chain codes information and recordpa**)

If the following appears, it means that the network has started successfully

 ![image-20200911122248915](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200911122248915.png)

<<<<<<< HEAD
Note: (1) If there is a timeout error when starting the network, it will not affect the starting of the network, and the system will automatically try 10 times, but if there is an obvious and serious error, stop the starting of the network at this time, and then execute. /start.sh down and then re-execute. /start.sh up to start the network, generally this kind of situation is not much, but there have been more serious timeout errors during the network startup process, just shut down the network and restart it.

(2) **Before each network startup, you need to execute ** **./start.sh down** **Clean up the network environment and start it to avoid errors! ! ! **

 

#### 3. Start the Node.js backend, including hospitals and patients

> Before starting the backend, please modify the **database** and **dbpassword** fields in the **config.json** file in the server directory to your own database and password (the name of the new database I created here is MedRec, password It is csj123zxc, so you only need to modify it to your own database name and password)

##### (1) Enter the specified directory to start the patient server:

 ![image-20200912141132896](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200912141132896.png)

##### (2) Enter the designated directory to start the hospital server:

![image-20200912141207135](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200912141207135.png)
	
**(3) Start IPFS local node**:

![image-20200912141423974](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200912141423974.png)

> Therefore, the deployment of this project requires four terminals: two back-end program nodes (simulating hospitals and patients respectively)	, one to start IPFS, and one to start the Fabric network

---

> #### The order of demonstration is strictly in accordance with the following order! ! !

##### (3) Visit the patient server

##### http://localhost:8081

![image-20200912110505902](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200912110505902.png)

 

**Click Register** to display the page, but at this time **Don't really register**, because we don't have a hospital yet

![image-20200912110607151](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200912110607151.png)

> The reason for visiting this registration page first is because, when writing the page before, the Fabric administrator user generation was placed on this page (this will be changed in the later order, this does not affect the use, just pay attention to the order of presentation That's it), after jumping to the registration page, the administrator admin will be generated, and the following ordinary users need to use the administrator when registering

At this point, enter the directory

> **/home/username/go/src/github.com/hyperledger/MedRec/App/server/Identity**
>
> You can see that there is an extra file **/wallet/admin**

 

##### (4) Visit the medical server and register the hospital	

http://localhost:3000/

![image-20200912110954024](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200912110954024.png)



Click **Register (note that the password must be 6 digits!!!)**

> (Note: When the hospital is the first people’s hospital, the user name must be h1; when the hospital is the second people’s hospital, the user name must be h2**, because this is written dead in the back end, and there is no Make changes, and there will be a chance to optimize again in the future. This does not affect the use, because this project is only done in two hospitals)

![image-20200914203640236](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914203640236.png)

We can see the **hospital1** folder in the wallet folder in **/home/username/go/src/github.com/hyperledger/MedRec/App/server/Identity**, which appears like this A few files can represent the successful registration of hospital1 (the first two files are RSA public and private keys, and the last three are identity files and private keys for fabric network communication):

![image-20200914164514316](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914164514316.png)


> After clicking sumit, enter the background, you can see (when registering for the first time, you may have to wait a while during the period, pay attention to the back-end terminal situation...until the back-end terminal shows the following **has been submit* *Only when the chain is successful)

![image-20200914164243277](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914164243277.png)

At this time, the chain is successful! ! !



**Then register hospital 2** (the same way as hospital 1 registration)

![image-20200914203820371](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914203820371.png)

 The same is the above steps, check whether the registration is successful, and on the chain.



##### (5) Register a patient (back to the patient server)

http://192.168.162.139:8081/goRegister

![image-20200914172756795](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914172756795.png)

> **Don't rush to do the operation after registration!!!!**, note that the operation in the background has not been completed yet, and the patient's public key is being wound on the chain. Wait until the chain is completed.

![image-20200914172903375](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914172903375.png)

And after registration, the page will jump directly to login (we can choose the corresponding hospital to log in directly)

![image-20200914173050301](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914173050301.png)

After clicking login, you will be redirected to the following page

![image-20200914173135468](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914173135468.png)

Complete patient personal information, click save

![image-20200914173507616](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914173507616.png)

The background information is as follows:

![image-20200914173525982](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914173525982.png)

Check that there will be a record in the patient table in the database

![image-20200914173558952](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914173558952.png)

At this time, the patient information is successfully registered, and then the consultation is in the left column. Click **Inquiry**, and then click **Cardiology**

![image-20200914173706625](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914173706625.png)

Fill in your own condition information and save it

![image-20200914173802247](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914173802247.png)

##### (6) Hospital login (access 3000 port)

http://192.168.162.139:3000/

![image-20200914173932678](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914173932678.png)

Fill in the user name **h1**, this means that we are logging in to hospital 1 (because the patient Hatano just described his condition in hospital 1, we are now looking at the situation under hospital 1 for consultation)

![image-20200914174101625](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914174101625.png)

 

After logging in, click **Diagnose Patient in Doctor's Workstation**

![image-20200914174502481](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914174502481.png)

You can see the patient’s previous visit record (condition description) of the patient Hatano in Hospital 1, click on this record

![image-20200914174538639](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914174538639.png)

Choose **symptoms**

![image-20200914174646200](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914174646200.png)

Click **Save**, and the prescription for the disease will appear at this time.

![image-20200914174819464](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914174819464.png)

Then **export case**

![image-20200914174937558](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914174937558.png)

At this point, we can see that the case file is generated in the **/home/username/go/src/github.com/hyperledger/MedRec/App/server/hospital-management-system-master/file** directory. 9.txt**

![image-20200914175142569](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-2020091417514256
9.png)



> Open it and you can see the patient’s condition and hospital diagnosis information as well as some other detailed information. We will upload this file to IPFS later.



Choose to upload medical records

![image-20200914175309732](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914175309732.png)

> **Note: qqq is Hatano's username**

![](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914180145627.png)

Click on the chain, you can see that the background is uploading the medical records to IPFS, and then the returned hash is uploaded to the chain, **wait a while! ! ! **The following is the success

![image-20200915134848811](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200915134848811.png)

At this point, the hospital uploads the patient’s diagnosed case information to IPFS and Fabric, and the patient can see the case after logging in

> If such an error occurs, it means that the identity is invalid and you need to log in to the hospital again 1.
>
> ![image-20200914180635240](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200914180635240.png)



##### (7) On the patient side, log in again to view the case uploaded by the hospital just now (according to your own information)

After logging in, click on the **Chain Medical Information** on the left (note that the name of the hospital is hospital1 instead of h1)

![image-20200915135344640](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200915135344640.png)

Click to view history, you can see that the patient has a historical case, which was uploaded by the hospital just now. You can view the case here and authorize other hospitals to access it (here is the patient’s **medical history query function and authorization function**)

![image-20200915135431900](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200915135431900.png)

Click on the hash address to view as follows: (Note: when viewing here, open a browser in the virtual machine to access the hash address link)

![image-20200915135753758](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200915135753758.png)

Then the patient clicks on the authorized case (here authorized hospital 2 is hospital2)

![image-20200915135843199](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200915135843199.png)

Click on authorization to view the background terminal:

![image-20200915135918945](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200915135918945.png)

Authorization succeeded! ! !



##### (8) Hospital 2 login (check authorized medical records)

![image-20200915140137983](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200915140137983.png)

Click View third-party cases in the doctor's workstation

![image-20200915140224557](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200915140224557.png)

![image-20200915140715050](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200915140715050.png)

View the medical record

![image-20200915140810121](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200915140810121.png)

Click the hash address link

![image-20200915140821695](https://cdn.jsdelivr.net/gh/VegeBun-csj/Images/image-20200915140821695.png)

You can see the authorized cases. At this point, the process of case sharing is over! ! !
