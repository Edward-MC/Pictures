# 1thing-crawler
Crawlers for 1thing project 

[Demo slide](https://go.zheng.ai/1t/sc)

------------

# User Manual

This is a user guide for beginners who has no background of python and crawler knowledges. If you feel hard to start with the project, please read the manual carefully. It will help you get started step by step. 

If you are very familiar with these knowledges, please feel free to quickly look through the commands and configurations, and start your funny journey right now !
<br><br><br>

## Create the virtual environment for the project

<br>

### What is virtualenv 
**The virtualenv is a tool to create isolated Python environments**. If you are developing more than one application at one time, and each application might need their own environment, for example, App A needs numpy-1.21.0 while App B needs numpy-1.18.0, you’ll find it troublesome to deal with the conflicts in your python environment. Even more, what if one of your projects needs python-3.6 or below, but another one needs python-3.8 or higher ? 

Virtualenv helps you solve the problems ! You can create virtual environments which are isolated from the base python environment. Different virtual environments can contain packages of different versions with no conflicts, which enables you to run applications in their suitable environments.

Note: Be careful with upgrading your base python, since the virtual environment will borrow things from your base python. For more details, please visit [Detailed Introductions for Virtualenv](https://virtualenv.pypa.io/en/latest/user_guide.html#introduction).

<br>

### Create a virtual environment yourself 
We have realized the benefits of virtual environments. Now, let’s have a try creating a virtual environment yourself. Before we start, we will assume you have already installed the python interpreter, and have the pip tool added to your system path.  

First, let’s start installing virtualenv.
```python
# Please make sure your pip is add to the system path. 
# If not sure, you can use pip –version to check
pip install virtualenv
```

This command will help you install the tool virtualenv into your computer, but after this, you need to **add virtualenv to your system path** if you find it is not there. Generally, for mac users, you can find the location of virtualenv at the end of the printed log. Remember to add this to your *.bash_profile* file.

For other methods, please visit [Virtualenv Installation](https://virtualenv.pypa.io/en/latest/installation.html#).



Then, we need to create a virtual environment for the project. It's necessary to find a place where you want to store it.

```python
# switch to the path you want to store virtual environment dictionary
# and excute the command
virtualenv environmentName
```

You can replace the *environmentName* to whichever name you like. Here, assume we use **venv** as our environment name. Virtualenv will help you create a virtual environment with your name provided. Again, you can install the packages you want, which will be independent with other environments.

For more detailed operations in this commands, please visit [CLI Interface](https://virtualenv.pypa.io/en/latest/cli_interface.html).

<br>

### Use your virtual environment
After installing venv, we can now switch between your base environment and venv by simply excute the following commands:

```python
# Before excuting the commands, we have to switch to the path you store the virtual environment
# For activating the venv:
source venv/bin/activate

# For deactivating the venv:
deactivate
```
<br>

### Install all the required packages

Under the project *1THING-CRAWLER*, there shall be a file called *requirement.txt*. This is the file where we integrate the dependencies of the project. We can simply use it to install all the required packages needed before running by executing the following command in your terminal:

```python
pip intsall -r requirements.txt
```
This command will help you to install all the packages specified in the requirement file so that you can use them directly in your virtual environment later.

If you want to know more details about pip install, please visit [Pip Install](https://pip.pypa.io/en/latest/cli/pip_install/).

<br><br>

## Install and Configure the Proxy Services

To avoid our ip being baned by the rules of the websites we crawl, we need `tor and privoxy services` to work as the 
proxies for us so that we can keep changing our IP while making requests. We will 
talk in detail next about how to install and configure them and how they will work in our project.

### Tor Service
The tor service is our key component to **make our IP changeable**. While requesting an url
by the tor network, it will help us hide the original IP, and give us a new IP address
as our identity to visit the url. This IP will not be same every time we use tor network.
In this way, we will not keep using the same IP to visit
the same url. What the website can see is that there are many different IPs coming to 
visit in a short time, but it will not identify this as a behavior against its rule, so 
we will not be baned.

#### Installation

###### For Mac Users
To install tor service, firstly, we need to install tools that behave like *apt-get* 
in Linux. Here we can choose `homebrew` instead. To install homebrew, please visit 
[Homebrew Installation](https://docs.brew.sh/Installation) for more details.

After the installation of homebrew, now we are ready to install tor service.

```
brew install tor
```
Typing the above commands in your terminal, you'll find that homebrew helps you
install tor in your computer. 

#### Configure Tor Service
For configuration, we need to find the configuration file called `torrc.sample` under the
dictionary of tor. Generally, in mac, it is located in `/usr/local/etc/tor/torrc.sample`.
If you are using mac M1 of Apple Silicon, you might find your file under `/opt/homebrew/etc/tor/`.

To configure the tor, move to the dictionary where torrc locates, and use vim to open torrc file in your terminal. 
```
vi torrc.sample
```

First, we need to set up a password, which could help us prevent attackers from 
accessing it. To do so, we can type:
```
tor --hash-password YOURPASSWORD
```
You can use any words you like to replace `YOURPASSWORD`, and you'll obtain the hash code
generated from your password whose format is like `16:XXXXXXXX`. 

Then, add the following lines to the bottom of your file, or uncomment the corresponding
lines.
```
ControlPort 9051
HashedControlPassword YOURPASSWORD
CookieAuthentication 1
```
Here, you'll need to replace `YOURPASSWORD` to the hash code you generated just now to
finish setting up the password to access the tor.


### Privoxy Service
As we described above, tor could help us to keep anonymous and keep from being baned
by the website rules. However, tor currently only support SOCK proxy. If we want to use HTTP/HTTPS
proxy here, we need another tool called `privoxy` to help transfer SOCK proxy to 
HTTP/HTTPS proxy.

#### Installation

###### For Mac Users
As we already set up homebrew, it is easy to install privoxy by:
```
brew install privoxy
```

#### Configure Privoxy Service
Under the same dictionary of tor, you can also find privoxy there. To configure,
move into the privoxy dictionary, and use vim to open the file `config`.
Add the following lines to the end of the file or uncomment the corresponding lines:
```
# The . in the end cannot be ignored
forward-socks5t / 127.0.0.1:9050 .

# Optional
keep-alive-timeout 600
default-server-timeout 600
socket-timeout 600
```
I recommend uncommenting the corresponding lines if you are not quite familiar and do not
want to delve deeper for this. In vim, you can simply type `/ words` and type Enter to search for words
you want. It behaves like `Ctrl + F` in your editor. 
If you want to just append them in your config file, copy and paste will be a great way
to keep from mistakes and errors.

Note: If you are new to vim operations, you can visit [Vim Operations](https://sites.radford.edu/~mhtay/CPSC120/VIM_Editor_Commands.htm)
or search in the internet for other resources.


### Make Them Run and Stop
To run them, we can simply type the following commands:
```
brew services start privoxy

# Use "brew services start tor" might not start tor successfully
# But you could try if it will work on your computer
# It does not work on my Mac of M1 chip ! 
tor controlport 9051
```

To test if they work, we can use:
```
# get your current IP
curl http://ifconfig.me 

# test Tor
torify curl http://ifconfig.me 

# test privoxy
curl -x 127.0.0.1:8118 https://ifconfig.me 
```
If the IPs you get from tor and privoxy are not the same with your current IP, it means
that you have successfully configured them. If not, please check carefully with all the above
steps to see if you have made some mistakes.

Finally, to stop them, type:
```
# stop privoxy
brew services stop privoxy

# get pid of the running tor in port 9051
lsof -i:9051

# kill tor process
kill PID
```
Please note that you should execute the second command first to get tor running pid, and
replace `PID` in the third command to kill the process. 


## Relations of Proxy Services (Optional)
The configurations we do for tor and privoxy are to connect each part together to correctly
perform what tor can do. We can directly treat privoxy and tor as a whole, but sometimes
we will meet some problems while configuring them, so it is better to realize how they
work together as a whole. In the following picture, we can clearly see how they cooperate together, and what they
do during the process. 

In `torrc.sample`, we add control port 9051(default 9051) and authentication. Here, we could
know that the port 9051 is the interface to communicate with scrapy project so that it could
use the function of tor. We can also know what the sock port(default 9050) do. It is the 
port where tor could communicate with other services by sock proxy. However, what we need
to visit the website is HTTP/HTTPS proxy, so we need privoxy to take sock proxy, and transform
to HTTP/HTTPS proxy.

In file `config`, we have set the forward method to port 9050 to chain tor and privoxy.
It makes sure that they can communicate with each other.

![Whole Process](https://github.com/Edward-MC/Pictures/blob/main/Process.png?raw=true)



------------
# News Hash Service
cd news_hash
1. Add the incident read from text.txt to the database: python testConn.py -add text.txt
2. Get all incidents from the database: python testConn.py -get

# SlackPipeline Service
A Scrapy ItemPipeline to send result item to Slack channel
## Settings
In `settings.py`:
```python
# The integer values you assign in this setting determine the order they run: items go through pipelines 
# from order number low to high. It’s customary to define these numbers in the 0-1000 range.
ITEM_PIPELINES = {
'c.pipelines.SlackPipeline': 301,
}

# Flag to open/close SlackPipeline service
SLACK_ENABLED = True
# Name of the slack channel
SLACK_CHANNEL = 'YOUR_SLACK_CHANNEL_HERE'
```
In your `Environment Variables`
```python
SLACK_BOT_TOKEN = 'YOUR_SLACK_BOT_TOKEN_HERE'
```
## How does it work
When a Scrapy Item arrives:
1. The pipeline will read crawler's settings and os environment variables
2. If SLACK_ENABLED is set to True, a slack client will be created by using slack-sdk and SLACK_BOT_TOKEN
3. The pipeline will parse item content into Slack message attachments. Then it utilizes the slack client and send 
   message to chosen channel(SLACK_CHANNEL)
4. If a SlackApiError happens, the pipeline will request crawler to stop and print error message.

