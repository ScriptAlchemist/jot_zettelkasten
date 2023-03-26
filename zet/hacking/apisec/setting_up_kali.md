# Setting Up Kali to Use Locally

> Always for educational purposes only

The method that we are going to use is `Oracle VM VirtualBox`. This is
a common method to practice and use for a virtual machine. Granted this
doesn't come with my security. It is connected to your systems. So you
should be careful what you do.

1. Download and setup Oracle VM VirtualBox
2. Go to the Kali Linux website and download the `.iso` image. Or the
   pre-built VM of your choosing. I'm using the VirtualBox version.
3. Up the RAM to `4096` and turn the networking into the `bridged`.
4. Turn on the VM and login using the root kali user.
  - Username: kali
  - Password: kali
5. Open up the terminal and update: `sudo apt update -y`.
6. Now run the upgrade `sudo apt upgrade -y`
7. Next we run `sudo apt dist-upgrade -y`

Now we are updated and ready to move on from here.

8. Update the user accounts
  - `sudo passwd kali (enter more complex password)`
  - `sudo useradd -m hker`
  - `sudo usermod -a -G sudo hker`
  - `sudo chsh -s /bin/zsh hker`

9. We will use the Burp Suite Community Edition. If it doesn't already
   exist we can add it with `sudo apt-get install burpsuite -y`

10. We must download `Jython` https://www.jython.org/download.html and
    add the `.jar` file to the Extender Options. This is needed to
    allow for add ons. Locate the `Python Environment` and the
    `location of Jython standalone JAR file`. Place the downloaded
    `.jar` file into this location.

11. We will then download `Autorize` from the `BApp Store`

12. Download FoxyProxy in the add on section. `Foxyproxy standard` was
    the choice I used. Add it and click `okay` on all the pop ups after
    you read them.

13. In the foxyproxy options panel. Add 2 proxies that we will be using.

  - Burp suite (8080)
    * Proxy IP address: `127.0.0.1`
    * port: `8080`
  - Postman
    * Proxy IP address: `127.0.0.1`
    * port: `5555`

14. Now we want to setup the burpsuite ca certificate. Use FoxyProxy to
    proxy with the Burpsuite option. Navigate to `http://burpsuite` and
    click on `CA Certificate` and this will download a `cacert.der`
    file.

15. Add this certificate into the firefox settings. Search for
    `certificate` in the search bar to find the location.

16. We can click on `view certificates` and import the new certificate
    you downloaded. `Trust this CA to identify websites: checked`,
    `Trust this CA to identify email users: checked`

The certificate should be setup now.

17. Let's install postman. For this we will use `sudo wget
    https://dl.pstmn.io/download/latest/linux64 -O
    postman-linux-x64.tar.gz`. This will download the `.tar`.

18. We will run `sudo tar -xvzf postman-linux-x64.tar.gz -C /opt`. This
    will open up the postman into the `/opt` folder for use.

19. 
