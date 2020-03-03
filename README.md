# geoblock-ipset-generator

A small python script that uses the free version of **IP2Location** database to generate huge ipsets based on [country codes](https://www.iban.com/country-codes) as described in the ISO 3166 international standard. The script can also be configured with the paid version of the database pretty easily with changing the name of the database file in the config.ini. It can be used alongside firewall software such as iptables to create rules based on the country origin of the requests.


# Features

 - Creating an ipset with specified name containing the IP ranges of specified countries
 - Database version auto-check to keep the ip ranges up to date
 - Database downloading of the free version (I don't have the paid version to figure out a way for it to work but you can update it manually)
 - Works across python versions (currently tested on python2.7 and 3.6)
 - (In development) Better integration of the auto-update for the paid version of the database

## Commands and parameters

Parameters:

    --countries <Alpha-2 ISO3166 Codes here separated by comma>
    --name <Name of the IPset>
    --update-database <Force an update of the ip database>

 
A general example:

    python geoblock.py --countries BG,UK --name custom-whitelist 
Database download example (will only work if you have a download token in the config):

    python geoblock.py --update-database

## Requirements and dependencies

 - A system running GNU/Linux
 - ipset
 - python
 - pip 

## Installation
***You might prefer to use virtual environments for your python projects in that case you would need to execute the following code between step 2 and 3:***

    python -m venv geoblock-ipset-generator
    source geoblock-ipset-generator/bin/activate


 1. Clone the repository

     git clone https://github.com/Ivstiv/geoblock-ipset-generator.git
 2. Enter and directory

     cd geoblock-ipset-generator
3. Install the required dependencies with pip

    pip install -r requirements.txt
4. Upload a database csv file or setup your download token in the config.ini. The database file can be found in **IP2LOCATION-LITE-DB1.BIN.ZIP** found [here](https://download.ip2location.com/lite/).
5. Run the script from the examples above.


## Setup a token for auto-version checking and database downloads
1. Make a free IP2Location account [here](https://lite.ip2location.com/sign-up) or [become their customer](https://www.ip2location.com/?rid=1522).
2. If you are using the lite(free) version you can find your download token [here](https://lite.ip2location.com/file-download).
3. Copy the **Download Token** to the **token field** in config.ini
4. Test it by forcing an update with --update-database

***Since I don't have the paid version I can't give you detailed instructions on how to do it but the process should be somewhat similar.***



## IPtables configurations of whitelists and blacklists
As an additional help I will leave a few iptables rules here for you to customise to your own situation. I didn't want to make them part of the script because this would only limit the use cases of the script. ***Please don't follow this if you have no experience with iptables and don't know how to revert your changes!***

Before we continue let me explain that the rules that use the PREROUTING chain are likely to affect any network namespaces or virtualisation technologies running on the system. In that case you should use the examples with the INPUT chain which is less likely to cause troubles. 

**You need to substitute any placeholders <> with your own values. And if you don't mind you can directly DROP the requests instead of REJECTING them. (check the -j flag)**

**WHITELIST**
[INPUT Chain] Implementing a whitelist:

    iptables -I INPUT -p tcp -j REJECT
    iptables -I INPUT -m set --match-set <IPSET_LIST> src -p tcp -j ACCEPT
[INPUT Chain] Implementing a whitelist on specific ports:

    iptables -I INPUT -p tcp -m multiport --dports <PORT1,PORT2,PORT3> -j REJECT
    iptables -I INPUT -m set --match-set <IPSET_LIST> src -p tcp -m multiport --dports <PORT1,PORT2,PORT3> -j ACCEPT

[PREROUTING Chain] Implementing a whitelist:

    iptables -t mangle -I PREROUTING -p tcp -j REJECT
    iptables -t mangle -I PREROUTING -m set --match-set <IPSET_LIST> src -p tcp -j ACCEPT
[PREROUTING Chain] Implementing a whitelist on specific ports:

    iptables -t mangle -I PREROUTING -p tcp -m multiport --dports <PORT1,PORT2,PORT3> -j REJECT
    iptables -t mangle -I PREROUTING -m set --match-set <IPSET_LIST> src -p tcp -m multiport --dports <PORT1,PORT2,PORT3> -j ACCEPT
**BLACKLIST**
[INPUT Chain] Implementing a blacklist (assuming default policy is ACCEPT):

    iptables -I INPUT -m set --match-set <IPSET_LIST> src -p tcp -j REJECT
    
  [INPUT Chain] Implementing a blacklist on specific ports (assuming default policy is ACCEPT)

    iptables -I INPUT -m set --match-set <IPSET_LIST> src -p tcp -m multiport --dports <PORT1,PORT2,PORT3> -j REJECT
    
[PREROUTING Chain] Implementing a blacklist (assuming default policy is ACCEPT):

    iptables -t mangle -I PREROUTING -m set --match-set <IPSET_LIST> src -p tcp -j REJECT

[PREROUTING Chain] Implementing a blacklist on specific ports(assuming default policy is ACCEPT):

    iptables -t mangle -I PREROUTING -m set --match-set <IPSET_LIST> src -p tcp -m multiport --dports <PORT1,PORT2,PORT3> -j REJECT

For any additional help consult the documentation of your firewall or check a tutorial on how iptables works. I recommend checking out [this one](https://www.booleanworld.com/depth-guide-iptables-linux-firewall/). 

# Links and additional info

I don't see how this can be further developed but if you have any ideas you are welcome to [join my discord](https://discord.gg/VMSDGVD) and ask for help or give me a heads up for problems with the script. I can't call myself a python developer by any means and this was developed entirely for personal use but I figured other people might find it useful as well so any feedback is appreciated!

