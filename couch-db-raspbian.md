CouchDB also has Fauxton, a native web-based interface built into CouchDB. It provides an interface to the majority of the CouchDB functionality, including the ability to create, update, delete and view documents and configuration parameters.

I’ve already written a blog post for installing CouchDB 2.0 under Rasbian Jessie for Raspberry Pi.  I’m updating my instructions for CouchDB 2.1 and Raspbian Stretch (in addition to Jessie). I’m presenting this “how-to” in coolbook form — i.e. just type in the commands as presented and you should end up with a functional CouchDB install.  For more background and info, check out my previous blog post.

[UPDATE: 03 April 2018] I have confirmed that my instructions work for CouchDB 2.1.1 and work under Raspbian Stretch on the new Raspberry Pi 3 Model B+.

You will need to obtain a URL for the zipped source.  You can get this from the CouchDB downloads page.  Go to this page and look for the latest source download section (currently: “Download CouchDB 2.1.0”).  Click on the red “Source” button and copy the suggested source mirror URL.  You’ll use this URL in the “wget” line below.

# update packages & OS
cd
sudo apt-get update
sudo apt-get upgrade

# You can check the OS version
cat /etc/os-release 

# add Erlang Solutions repository and public key
wget http://packages.erlang-solutions.com/debian/erlang_solutions.asc
sudo apt-key add erlang_solutions.asc
sudo apt-get update

# install all build dependencies - note mutiple lines
sudo apt-get --no-install-recommends -y install \
build-essential pkg-config erlang libicu-dev \
libmozjs185-dev libcurl4-openssl-dev

#add couchdb user and home
sudo useradd -d /home/couchdb couchdb
sudo mkdir /home/couchdb
sudo chown couchdb:couchdb /home/couchdb

# Get source - need URL for mirror (see blog instructions)
wget http://mirror.stjschools.org/public/apache/couchdb/source/2.1.0/apache-couchdb-2.1.0.tar.gz

# extract source and enter source directory
tar zxvf apache-couchdb-2.1.0.tar.gz 
cd apache-couchdb-2.1.0/

# configure build and make executable(s)
./configure
make release

#copy built release to couchdb user home directory
cd ./rel/couchdb/
sudo cp -Rp * /home/couchdb
sudo chown -R couchdb:couchdb /home/couchdb
cd /home/couchdb/etc

# need to edit IP address to allow external access
sudo vi local.ini

 

After building and installing CouchDB, you can configure it to allow connections from external IP addresses.  You need to edit “/home/couchdb/etc/local.ini”.  The “sudo vi local.ini” line above.

Change this line:
 #bind_address = 127.0.0.1
 to:
 bind_address = 0.0.0.0

You can now run couchdb as user: couchdb:

sudo -i -u couchdb /home/couchdb/bin/couchdb

Connect to CouchDB’s Fauxton GUI using your favorite browser using the URL:

http://192.168.0.27:5984/_utils/

Replace my IP (192.168.0.27) with your R-Pi’s.  Make sure you follow the first-time setup instruction in the CouchDB documentation!  Most users will want/need a single node setup.  You can do this by clicking on the wrench icon in Fauxton and selecting “single node”.  You can bind to your R-Pi’s external IP if you wish to connect via a browser on other computers.  I recommend restarting your browser after initial setup to ensure everything is working properly.
