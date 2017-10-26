=================================
2xml - plaintext to XML converter
=================================

    
Tabular data can be converted to other format with standard utilities
and only couple of lines of code. ``2xml`` is somewhat naive but at 
the same time it can serve as demonstration for flexibility offered 
by Unix philosophy.

.. code::

    $ head -6 ../samples/df.txt
    # meta prog:/bin/df 
    #
    Filesystem                    Blocks_1024      Used Available Capacity Mounted
    udev                              1837416         0   1837416        0 /dev
    tmpfs                              371560      5956    365604        2 /run
    /dev/sdx3                        19554584  10289968   8248248       56 /


    $ head -6 ../samples/df.txt | ./2xml 
    <?xml version="1.0" encoding="ISO-8859-1" ?>
    <!DOCTYPE df_list SYSTEM "cof.dtd">
    <df_list>
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
      <df_entry>
    	<Filesystem>/dev/sdx3</Filesystem>
    	<Blocks_1024>19554584</Blocks_1024>
    	<Used>10289968</Used>
    	<Available>8248248</Available>
    	<Capacity>56</Capacity>
    	<Mounted>/</Mounted>
      </df_entry>
    </df_list>



    $ head -6 ../samples/df.txt | ./2xml | xmllint --loaddtd --noout -
    $ echo $?
    0

    $ head -6 ../samples/df.txt | ./2xml | xmllint --schema cof.xsd --noout -
    - validates

