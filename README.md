Mediacom email quota via SMS
----------------------------

This project is towards being able to receive via SMS your projected Mediacom internet usage and cost by leveraging Linear Regression and your email account (in this case I use gmail).

In order for this to work you will need to download the packages below.  Specially for Selenium to work, you will need Chrome to be installed.  That way the display could project properly.

This project has been tested on macOS and Ubuntu (server on docker).

## Running the code in prod

### Prerequisites
To execute this program properly, you will need to have the following program installed on your environment.

-   python 3.5
-   Chrome 56
-   Xvfb

Tested on macOS and Ubuntu.

### How to use it

Before you start anything there are some basic config that you will need to do.  On the main project path, run the command below to create a config file.  You will need to populate the variables to the right of the equal sign with your credentials.
```
echo 'isp_usr=YOUREMAIL@mediacombb.net
isp_pwd=YOURPASSWORD
gmail_usr=YOUREMAIL@gmail.com
gmail_pwd=YOUR_GMAIL_PASSWORD' > conf
```

To run the main code you will need to make sure that you have your dev environment configured or do a docker image.

If you are using your host (not docker), then you can do the following
``` python
python main.py chromedriver_mac 5557779999@vtext.com
```

Sending to more than one address.
``` python
python main.py chromedriver_mac 5557779999@vtext.com,2224446666@vtext.com
```

To automate delivery of the SMS, than you can setup a crontab and send it every so often.  In this case, it will run at 6a and 6p everyday.
```
crontab -e
* 6,18 * * * python /path/to/project/email2sms/main.py chromedriver_linux64 5557779999@vtext.com > /dev/null 2>&1 #gather and email mediacom quota via sms
```

### Using docker
If you are using docker, then you can do the following.  This will run, mount, and delete the docker container once done running.
``` bash
docker run --rm -d -P -v /path/to/project/email2sms:/src/email2sms blacknred0/mediacomsmsquota 5557779999@vtext.com
```

## Dev - Install packages & config env

### Non-docker way
These are the tools and configuration that you will need if you want to do testing on your side.

1.  Download [Chrome](https://www.google.com/chrome/browser/)
2.  Download [Python](https://www.python.org/) and config environment
``` python
pip install numpy scipy matplotlib sympy nose pandas sklearn selenium pyvirtualdisplay
```

If you want to do your testing in Ubuntu (16.04 LTS), you will need to do the following.

```
sudo apt -y install wget python3 libxss1 libappindicator1 libindicator7 xvfb
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
sudo dpkg -i google-chrome*.deb
sudo apt install -f -y #due to missing dependencies, then we will force and install them
sudo dpkg -i google-chrome*.deb

sudo ln /usr/bin/python3.5 /usr/bin/python
wget https://bootstrap.pypa.io/get-pip.py
python get.pip.py
```

Once you run `python get.pip.py`, then you can go to step 2 from above by setting up "Python environment".


### Docker way (using Ubuntu)
#### Lazy
TBF (To Be Fill)

#### From scratch
Download and configure your docker image.
```
#download & conf docker
docker pull ubuntu #by default it will use latest LTS
docker run -it ubuntu
apt update

#install dependencies, chrome, and config environment
apt -y install wget python3 libxss1 libappindicator1 libindicator7 xvfb
wget https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb
dpkg -i google-chrome*.deb
apt install -f -y #might be needed due to missing dependencies, then we will force and install them
dpkg -i google-chrome*.deb
ln /usr/bin/python3.5 /usr/bin/python
wget https://bootstrap.pypa.io/get-pip.py
get.pip.py
pip install numpy scipy matplotlib sympy nose pandas sklearn selenium pyvirtualdisplay
rm google-chrome*.deb get.pip.py
exit
```

Now, that you have your image ready, let's commit.  Make sure that you replace "5d2c74af649f" with your docker image id.
```
docker commit -m "Configure the whole environment to run" -a "First Last Names" 5d2c74af649f blacknred0/mediacomsmsquota
```

##### Building docker image
All files and programs are ready for prime time.  Make sure that you are on the main path location of the project and run the following to build your docker image.

```
docker build --rm -t blacknred0/mediacomsmsquota .
```

This will run, mount, and delete the docker container once done running.
``` bash
docker run --rm -d -P -v /path/to/project/email2sms:/src/email2sms blacknred0/mediacomsmsquota 5557779999@vtext.com
```

To view the image with removing the docker container once you are done.
```
docker run --rm -it blacknred0/mediacomsmsquota bash
```

## Some sources/resources

### Carriers
Below is the mapping table of the most common United States carriers and their gateway domain. You will need to place in front of the @ sign your ten digit phone number to where you are sending the message to.

For example, if my phone number is 555-777-9999 and I use Verizon, then I will be sending a SMS to `5557779999@vtext.com`.

| Carrier   |      SMS      |   MMS  |
|-----------|:-------------:|:------:|
| Alltel | @message.alltel.com | @mms.alltelwireless.com |
| AT&T | @txt.att.net | @mms.att.net |
| Boost Mobile | @myboostmobile.com | @myboostmobile.com |
| Cricket Wireless | @mms.cricketwireless.net |  |
| Project Fi | @msg.fi.google.com |  |
| Sprint | @messaging.sprintpcs.com | @pm.sprint.com |
| T-Mobile | @tmomail.net | @tmomail.net |
| U.S. Cellular | @email.uscc.net | @mms.uscc.net |
| Verizon | @vtext.com | @vzwpix.com |
| Virgin Mobile | @vmobl.com | @vmpix.com |
| Republic Wireless | @text.republicwireless.com |  |

### Links
[Selenium documentation](http://selenium-python.readthedocs.io/installation.html)

[Table of carrier email to SMS](http://www.digitaltrends.com/mobile/how-to-send-e-mail-to-sms-text/)