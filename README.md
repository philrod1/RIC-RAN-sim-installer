#### It is assumed that the OAIC RIC and srsRAN components have already been installed: -
### Part 1: https://github.com/philrod1/oaic-ric-installer
### Part 2: https://github.com/philrod1/srsRAN-installer

    
## Refresh apt and install stuff

    message () { echo -e "\e[1;93m$1\e[0m"; }
    message "Refresh apt"
    sudo apt update
    sudo apt upgrade -y
    sudo apt install -y python3-pip npm
    pip3 install websockets



## Install Ansible

    message "Install Ansible"
    sudo python3 -m pip install ansible


## Install Ricmon

    message "Installing Ricmon"
    cd ~
    git clone -b srsRAN https://github.com/philrod1/ricmon.git
    cd ricmon
    npm install
    sed -i "s/[0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+:32080/$KONG_PROXY:32080/g" ./routes/index.js
    sed -i "s/[0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+:8080/$APPMGR_HTTP:8080/g" ./routes/index.js
    sed -i "s/[0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+:3800/$E2MGR_HTTP:3800/g" ./routes/index.js
    message "Ricmon installed.  Start it with 'npm start' from within the ricmon directory."


## Copy scripts and config files

    message "Copying scripts and config files"
