# Part 3 of the OAIC RIC + srsRAN + Simulator setup
#### This README file is also the script that does all of the things.  You can run it with this command: -
#### curl -L https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/README.md | bash
#### Alternatively, you can click on the 🖉 symbol in Github and copy the raw markdown.
#### You could also run each section by using the copy option

## It is assumed that the OAIC RIC and srsRAN components have already been installed: -
### Part 1: https://github.com/philrod1/oaic-ric-installer
### Part 2: https://github.com/philrod1/srsRAN-installer

## Refresh apt and install stuff

    message () { echo -e "\e[1;93m$1\e[0m"; }
    message "Refresh apt"
    sudo apt update
    sudo apt upgrade -y
    sudo apt install -y python3-pip npm iperf3
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
    sed -i "s/[0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+:8080\/ric/$APPMGR_HTTP:8080\/ric/g" ./routes/index.js
    sed -i "s/[0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+:8080\/api/$ONBOARDER_HTTP:8080\/api/g" ./routes/index.js
    sed -i "s/[0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+:3800/$E2MGR_HTTP:3800/g" ./routes/index.js
    sed -i "s/[0-9]\+\.[0-9]\+\.[0-9]\+\.[0-9]\+:8765/$myip:8765/g" ./public/javascripts/sketch.js


## Get scripts

    message "Getting scripts"
    cd ~
    mkdir scripts
    mkdir iperf
    cd scripts
    wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/killAllThings.sh
    wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/iperf.yml
    wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/srs.yml
    wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/startClient.sh
    wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/startServer.sh
    wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/startENB.sh
    wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/startUE.sh
    wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/stopIperf.sh
    wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/stopSRS.sh
    wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/radio.py
    sed -i "s|\$HOME|$HOME|g" srs.yml
    sed -i "s|\$HOME|$HOME|g" iperf.yml
    sed -i "s|\$HOME|$HOME|g" startServer.sh
    sed -i "s|\"\$E2TERM\"|$E2TERM|g" startENB.sh
    sed -i "s|\"\$myip\"|$myip|g" startENB.sh
    chmod +x *.sh


## Get configs

    message "Getting configs"
    sudo wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/config/enb.conf -O /root/.config/srsran/enb.conf
    sudo wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/config/epc.conf -O /root/.config/srsran/epc.conf
    sudo wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/config/mbms.conf -O /root/.config/srsran/mbms.conf
    sudo wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/config/rb.conf -O /root/.config/srsran/rb.conf
    sudo wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/config/rr.conf -O /root/.config/srsran/rr.conf
    sudo wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/config/sib.conf -O /root/.config/srsran/sib.conf
    sudo wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/config/ue.conf -O /root/.config/srsran/ue.conf
    sudo wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/config/slice_db.csv -O /root/.config/srsran/slice_db.csv
    sudo wget https://raw.githubusercontent.com/philrod1/RIC-RAN-sim-installer/main/config/user_db.csv -O /root/.config/srsran/user_db.csv


## What now?
### Start the Ricmon web app (in a screen) from inside ~/ricmon with ``npm start``.  Open http://localhost:3000 or http://<ip-address>:3000
### Start the things!  From inside ~/scripts
#### Start srsRAN components with ``sudo ansible-playbook srs.yml``
#### Start the radio with ``python3 radio.py``
#### Check in the srs logs in Ricmon.  The UEs should be assigned IP addresses.  If not, try again.
#### Start the iperf servers and clients with ``sudo ansible-playbook iperf.yml``
#### Check the SIM in Ricmon.  You should see traffic indicated in the guages

## Go again?
### This stuff seems flaky and prone to failure.  The start-up routine needs to be done correctly to have any hope.
### Restarting srsRAN is often required.  So often, in fact, that I made a script to help.
#### First, stop the radio script with Ctrl-C
#### Then, run the kill script from the scripts directory ``sudo ./killAllThings.sh``
