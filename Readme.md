# Jupyter Guide 

### Author : Christian Glassiognon

##### [Follow me on twitter](https://Twitter.com/_heyglassy)

## Pre-requisites : Setting Up EC2

To set up our remote Jupyter setup, regardless if we access the notebook via SSH or HTTPS or use the Jupyter Kernel Gateway, we need an EC2 instance. 

If you don’t have an AWS account, create one and enable MFA to keep your account secure. Next, navigate to the EC2 page and click launch instance. Select “Ubuntu 20.04” for your OS, select your instance type (t2.micro in this guide), and click on “Review Instance and Launch.” Ensure that your settings are to your liking, and then click on “Launch.” Clicking Launch should show you a popup to create or use an existing key pair. These key pairs authenticate and authorize your EC2 instance via SSH, create a new pair and download the .pem file onto your computer. I recommend you put this file onto the root directory of your terminal for easy access. 

Once your EC2 instance is ready, click on the instance details and find the “Public IPv4 DNS”, which will look something like this “ ec2-00-000-000-000.compute-1.amazonaws.com”. Copy this address onto your clipboard and open your terminal. Before we connect to our instance, we need to ensure that our key is not publically accessible. In your terminal, navigate to the directory that contains your key file and run ` chmod 400 <filename>` . Now, we will SSH into our server, run `ssh -i <keyname>.pen ubuntu@<ec2 address>` and enter yes when asked about the host authenticity. The “-i” of the command that we just ran gives SSH the key that will be checked against the private key, giving us authorization to access our EC2 instance. 

You should now see something like this.

![](https://i.imgur.com/PpTdWHy.png)





Now that we’ve connected to our server, we need to install Juypter and the Jupyter Kernel Gateway. Let’s start by updating our package installer. Run `sudo apt-get update`, this will install python and other necessary packages needed to run Jupyter. Now run `sudo apt install python3-pip` and `pip3 install --upgrade pip` which gives us pip, our python package installer. Run `pip3 -V` to ensure you've successfully installed pip, you should see something like this. 

![](https://i.imgur.com/UTZjA0l.png)

Once you've installed and updated pip, we can install the jupyter notebook and kernel gateway.

## Pre-requisites : Installing Jupyter Notebook & Kernel Gateway

In your EC2 instance, lets upgrade pip again before installing our jupyter notebook and kernel gateway, this ensures that the latest packages will be downloaded and used, to do that run `pip3 install --upgrade pip` . Now to install the jupyter notebook and kernel gateway via pip, run `pip install notebook` and `pip install jupyter_kernel_gateway` . Once this is done, we need to update our shell so that it can find all the new packages we've installed, to do this, run `source ~/.profile` . 

Then ensure the jupyter notebook has been installed correctly by running `jupyter notebook` -v, you should see something like this.

![](https://i.imgur.com/DMyrMo3.png)

Then ensure the jupyter kernel gateway has been installed correctly by running `jupyter kernelgateway -v`, you should see something like this.

![](https://i.imgur.com/pB2Zx1e.png)

## Accessing Jupyter Notebooks via SSH

Once we have installed our pre-requisite packages, we can now SSH into a Jupyter notebook running on our instance via SSH. The command to do so is similar to SSH-ing into our server in general; this time, we will SSH into the port hosting our notebook instead of the default port 20. Before we run this command, let’s start our Jupyter Notebook on the server. Then, via SSH, run `jupyter notebook`, this should launch the notebook on port 8888. 

![](https://i.imgur.com/f1N1Y3a.png)



Once you see that the server has started, copy the localhost URL with the full token, we will use this in just a moment to access our notebook. Now open another terminal instance on your desktop, and navigate to the directory that contains your key. Run the command `ssh -i <keyname>.pem ubuntu@<ec2 public ipv4 dns url> -NL 8888:localhost:8888`. The “L” in our command tells ssh to forward a locally hosted port on the client to a local port on our server. Specifying which ports to use locally and on the server follows this syntax `<localport>:<remotehost>:<remoteport>`. The “N” in the command tells ssh not to execute a remote command, allowing us just to forward our port to our desktop. Now that you’ve started your notebook and forwarded any requests from your desktops localhost:8888 to your servers localhost:8888, open a browser and the localhost url + token given by Jupyter into the address bar. You should now be able to access your Jupyter notebook!

![](https://i.imgur.com/zpOUrKz.png)

> **Note** 
>
> If you decide to use the Kernel Gateway to compute your code, specify this via the `--gateway-url=<url>` option when starting your notebook.

## Accessing Jupyter Notebooks via HTTP

Once we have installed our pre-requisite packages, we can now host our Jupyter notebook on a public IP address and access it via a browser over HTTP. By default, Juypter hosts its notebook on the localhost and does not accept requests from any other IP, including our instance’s private IP. To accept requests via HTTP, we need to tell the notebook to use the EC2 instances private IP address to access the notebook via the public IP address AWS gives up. Before we start the server, find the private ip address in the EC2 instance details. Then start the notebook using this command `jupyter notebook --ip=<private ip address>`. Once you have done this, enter `<public ipv4 dns url>:<default notebook port(8888)>/?token=<token given on notebook startup>` into your browser’s address bar. You should now be able to access your jupyter notebook!

![](https://i.imgur.com/zpOUrKz.png)

> **Note** 
>
> If you decide to use the Kernel Gateway to compute your code, specify this via the `--gateway-url=<url>` option when starting your notebook.



## Jupyter Kernel Gateway

The Jupyter Kernel Gateway allows you to separate your Kernels, the processes that run your code, and your notebook, the IDE you write your code in. Kernel Gateway will enable you to scale your Kernels’ processing more easily and gives you more flexibility of what types of Kernels you can run. It also allows you to run your notebooks locally and use remote network resources instead of hosting your notebooks on the server. 

You can start the Kernel Gateway in the same ways you’ve created the notebook on the public IP address. Follow those steps but replace “notebook” with `kernelgateway`. Verifying that the server is working properly can be done by using curl or postman and getting `<public ipv4 dns url>:<default gateway port(8888)>/api/kernelspecs` on your desktop.

![](https://i.imgur.com/tKZn7qL.png)





