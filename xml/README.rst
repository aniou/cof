=================================
2xml - plaintext to XML converter
=================================
    
Tabular data can be converted to other format with standard utilities
and only couple of lines of code. ``2xml`` is somewhat naive but at 
the same time it can serve as demonstration for flexibility offered 
by Unix philosophy.

.. code::

    $ head -5 -q ../samples/df.txt ../samples/arp.txt  
    # meta prog:df 
    #
    Filesystem                    Blocks_1024      Used Available Capacity Mounted
    udev                              1837416         0   1837416        0 /dev
    tmpfs                              371560      5956    365604        2 /run
    # meta prog:arp ver:2
    #
    Host  IPv4        MAC                Interface
    -     1.2.3.2     00:11:32:cc:aa:bb  wm0
    -     1.2.3.1     aa:aa:bb:20:1a:cb  wm0



    $ head -5 -q ../samples/df.txt ../samples/arp.txt  | ./2xml 
    <?xml version="1.0" encoding="ISO-8859-1" ?>
    <cof
      xmlns="http://smutek.pl/cof"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
      xsi:schemaLocation="http://smutek.pl.cof cof.xsd">
      <df_entry>
        <Filesystem>udev</Filesystem>
        <Blocks_1024>1837416</Blocks_1024>
        <Used>0</Used>
        <Available>1837416</Available>
        <Capacity>0</Capacity>
        <Mounted>/dev</Mounted>
      </df_entry>
      <df_entry>
        <Filesystem>tmpfs</Filesystem>
        <Blocks_1024>371560</Blocks_1024>
        <Used>5956</Used>
        <Available>365604</Available>
        <Capacity>2</Capacity>
        <Mounted>/run</Mounted>
      </df_entry>
      <arp_entry>
        <Host>-</Host>
        <IPv4>1.2.3.2</IPv4>
        <MAC>00:11:32:cc:aa:bb</MAC>
        <Interface>wm0</Interface>
      </arp_entry>
      <arp_entry>
        <Host>-</Host>
        <IPv4>1.2.3.1</IPv4>
        <MAC>aa:aa:bb:20:1a:cb</MAC>
        <Interface>wm0</Interface>
      </arp_entry>
    </cof>


    $ head -5 -q ../samples/df.txt ../samples/arp.txt | ./2xml | xmllint --noout --schema cof.xsd -
    - validates


