#!/bin/bash

LOGFILE=/usr/src/script.log
ERRORFILE=/usr/src/errors.log
PJSIP=http://www.pjsip.org/release/2.7.2/pjproject-2.7.2.tar.bz2
JANSSON=https://github.com/akheron/jansson/archive/v2.11.tar.gz
ASTERISK=http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-15-current.tar.gz

# Install needed programs
array=( dialog openssh-server mc build-essential dh-autoreconf )

echo "Start check if programs is installed!"

for i in "${array[@]}"
do
   echo $i
   if [ $(dpkg-query -W -f='${Status}'  $i 2>/dev/null | grep -c "ok installed") -eq 0 ];
   then
     apt-get -y install $i
   fi
done



cd /usr/src
# Not needed ?!
# wget http://downloads.asterisk.org/pub/telephony/dahdi-linux-complete/dahdi-linux-complete-current.tar.gz

echo "----- Get files needed for installing Asterisk -----"
echo "----- pjproject -----"
if ! [ -f /usr/src/pjproject.tar.bz2 ]
    then wget -O pjproject.tar.bz2 $PJSIP -q --show-progress
    # then wget -O pjproject.tar.bz2 http://www.pjsip.org/release/2.7.2/pjproject-2.7.2.tar.bz2 -q --show-progress
fi

echo "----- jansson -----"
echo "----- Fetch jansson -----" >> $LOGFILE
if ! [ -f /usr/src/jansson.tar.gz ]
    then wget -O jansson.tar.gz $JANSSON -q --show-progress
    # then wget -O jansson.tar.gz https://github.com/akheron/jansson/archive/v2.11.tar.gz -q --show-progress
fi
echo "----- Asterisk -----"
echo "----- Fetch Asterisk -----" >> $LOGFILE
if ! [ -f /usr/src/asterisk-15.tar.gz ]
    then wget -O asterisk-15.tar.gz $ASTERISK -q --show-progress
    # then wget -O asterisk-15.tar.gz http://downloads.asterisk.org/pub/telephony/asterisk/asterisk-15-current.tar.gz -q --show-progress
fi

dialog --pause 'Download OK!' 10 60 10 --


echo "Compile and install pjproject"
echo "--------------------------------" >>$LOGFILE
echo "Compile and install pjproject" >>$LOGFILE
echo "--------------------------------" >>$LOGFILE
cd /usr/src
mkdir /usr/src/pjproject
tar xjvf pjproject.tar.bz2 --directory /usr/src/pjproject --strip-components=1
cd pjproject
CFLAGS='-DPJ_HAS_IPV6=1' ./configure --enable-shared --disable-sound --disable-resample --disable-video --disable-opencore-amr
make dep && make && make install

dialog --pause 'Jansson!' 10 60 10 --


echo "Compile and install jansson"
echo "--------------------------------" >>$LOGFILE
echo "Compile and install jansson" >>$LOGFILE
echo "--------------------------------" >>$LOGFILE
cd /usr/src
mkdir /usr/src/jansson
tar xvfz jansson.tar.gz --directory /usr/src/jansson --strip-components=1
cd jansson
autoreconf -i
./configure
make && make install && ldconfig

dialog --pause 'Asterisk!' 10 60 10 --

echo "Install Asterisk"
echo "--------------------------------" >>$LOGFILE
echo "Install Asterisk" >>$LOGFILE
echo "--------------------------------" >>$LOGFILE
cd /usr/src
mkdir /usr/src/asterisk-15
tar xvfz asterisk-15.tar.gz --directory /usr/src/asterisk-15 --strip-components=1
cd asterisk-15

dialog --pause 'Asterisk Contrib!' 10 60 10 --

contrib/scripts/get_mp3_source.sh
contrib/scripts/install_prereq install
./configure

dialog --pause 'Asterisk --make menuselect-- and then compile!' 10 60 10 --
