common output format for unix tools
===================================

See WIKI for latest draft.

Installation instructions (NetBSD 8)
------------------------------------

    localhost# pkg_add python36
    localhost# pkg_add py36-pip
    localhost# pkg_add py36-six 
    
    localhost$ pip3.6 install --user parsimonious
    localhost$ pip3.6 install --user termcolor

Usage
-----
 
    $ cat df.txt | ./sel 1,2,Used,Used where Used -eq 0
    # meta prog:/bin/df
    #
    Filesystem 1024-blocks Used Used      
    udev       1837416     0    0         
    tmpfs      1857800     0    0         
    
    $ cat df.txt | ./sel -f 1,2,Used,Used -w Used -gt 0
    # meta prog:/bin/df
    #
    Filesystem             1024-blocks Used      Used      
    tmpfs                  371560      5956      5956      
    /dev/sdx3              19554584    10289968  10289968  
    tmpfs                  1857800     56916     56916     
    tmpfs                  5120        4         4         
    /dev/sdx2              123723748   10807024  10807024  
    1.2.3.4:/foobar2/test1 956688640   740075264 740075264 
    1.2.3.4:/foobar2/test2 956688640   740075264 740075264 
    1.2.3.4:/foobar2/test9 956688640   740075264 740075264 
    tmpfs                  371560      36        36       
    
    
    $ cat df.txt | ./sel -f 1,2,Used,Used,Mounted -w Used -gt 0 -a Mounted -starts /rr
    # meta prog:/bin/df
    #
    Filesystem             1024-blocks Used      Used      Mounted   
    /dev/sdx2              123723748   10807024  10807024  /rr/rrr   
    1.2.3.4:/foobar2/test1 956688640   740075264 740075264 /rr/test1 
    1.2.3.4:/foobar2/test2 956688640   740075264 740075264 /rr/test2 
    1.2.3.4:/foobar2/test9 956688640   740075264 740075264 /rr/test9 

    
