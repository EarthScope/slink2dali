# <p >slink2dali 
###  SeedLink to DataLink</p>

1. [Name](#)
1. [Synopsis](#synopsis)
1. [Description](#description)
1. [Options](#options)
1. [Seedlink Selectors](#seedlink-selectors)
1. [Stream List File](#stream-list-file)
1. [Caveats](#caveats)
1. [Author](#author)

## <a id='synopsis'>Synopsis</a>

<pre >
slink2dali [options] slhost dlhost
</pre>

## <a id='description'>Description</a>

<p ><b>slink2dali</b> connects to a <u>SeedLink</u> server, requests data streams and forwards the received packets to a <u>DataLink</u> server.</p>

<p >This program is designed to run continuously.  Because the SeedLink and DataLink protocols are stateful this program should be very tolerant of connection breaks and subsequent re-connections.</p>

## <a id='options'>Options</a>

<b>-V</b>

<p style="padding-left: 30px;">Print program version and exit.</p>

<b>-h</b>

<p style="padding-left: 30px;">Print program usage and exit.</p>

<b>-v</b>

<p style="padding-left: 30px;">Be more verbose.  This flag can be used multiple times ("-v -v" or "-vv") for more verbosity.</p>

<b>-d</b>

<p style="padding-left: 30px;">Configure the SeedLink connection in "dial-up" mode.  The remote server will close the connection when it has sent all of the data in it's buffers for the selected data streams.  This program will then exit cleanly and save the connection state if a state file has been specified.</p>

<b>-N </b><u>netcode</u>

<p style="padding-left: 30px;">Change the SEED network code to <u>netcode</u> in all records forwarded.</p>

<b>-x </b><u>statefile</u>[:<u>interval</u>]

<p style="padding-left: 30px;">During client shutdown the last received sequence numbers and time stamps (start times) for each data stream will be saved in this file. If this file exists upon startup the information will be used to resume the data streams from the point at which they were stopped.  In this way the client can be stopped and started without data loss, assuming the data are still available on the server.  If <u>interval</u> is specified the state will be saved every <u>interval</u> packets that are received.  Otherwise the state will be saved only on normal program termination.</p>

<b>-s </b><u>selectors</u>

<p style="padding-left: 30px;">This defines default selectors.  If no multi-station data streams are configured these selectors will be used for uni/all-station mode. Otherwise these selectors will be used when no selectors are specified for a given stream using the '-S' or '-l' options.</p>

<b>-l </b><u>listfile</u>

<p style="padding-left: 30px;">A list of streams will be read from the given file.  This option implies multi-station mode.  The format of the stream list file is given below in the section <u>Stream list file</u>.</p>

<b>-S </b><u>stream[:selectors],...</u>  (requires SeedLink >= 2.5)

<p style="padding-left: 30px;">The connection will be configured in multi-station mode with optional SeedLink selectors for each station, see examples below.  <u>stream</u> should be provided in NET_STA format.  If supported by the server the '*' or '?' wildcards may be used in the NET_STA designation.  If no selectors are provided for a given stream, the default selectors, if defined, will be used.</p>

<b>-tw </b><u>start:[end]</u>  (requires SeedLink >= 3)

<p style="padding-left: 30px;">Specifies a time window applied, by the server, to data streams.  The format for both times is year,month,day,hour,min,sec; for example: "2002,08,05,14,00:2002,08,05,14,15,00".  The end time is optional but the colon must be present.  If no end time is specified the server will send data indefinately.  This option will override any saved state information.</p>

<p style="padding-left: 30px;">Warning: time windowing might be disabled on the remote server.</p>

<b>-nd </b><u>delay</u>

<p style="padding-left: 30px;">The network reconnect delay (in seconds) for the connection to the SeedLink server.  If the connection breaks for any reason this will govern how soon a reconnection should be attempted. The default value is 30 seconds.</p>

<b>-nt </b><u>timeout</u>

<p style="padding-left: 30px;">The network timeout (in seconds) for the connection to the SeedLink server.  If no data [or keep alive packets?] are received in this time the connection is closed and re-established (after the reconnect delay has expired).  The default value is 600 seconds. A value of 0 disables the timeout.</p>

<b>-k </b><u>keepalive</u>  (requires SeedLink >= 3)

<p style="padding-left: 30px;">Keepalive packet interval (in seconds) at which keepalive (heartbeat) packets are sent to the server.  Keepalive packets are only sent if nothing is received within the interval.</p>

<b></b><u>slhost</u>

<p style="padding-left: 30px;">Specifies the address of the SeedLink server in host:port format. Either the host, port or both can be omitted.  If host is omitted then localhost is assumed, i.e.  ':18000' implies 'localhost:18000'.  If the port is omitted then 18000 is assumed, i.e.  'localhost' implies 'localhost:18000'.  If only ':' is specified 'localhost:18000' is assumed.</p>

<b></b><u>dlhost</u>

<p style="padding-left: 30px;">Specifies the address of the DataLink server in host:port format. Either the host, port or both can be omitted.  If host is omitted then localhost is assumed, i.e.  ':16000' implies 'localhost:16000'.  If the port is omitted then 16000 is assumed, i.e.  'localhost' implies 'localhost:16000'.  If only ':' is specified 'localhost:16000' is assumed.</p>

## <a id='seedlink-selectors'>Seedlink Selectors</a>

<p ><u>Notes regarding selectors from a SeedLink configuration file</u></p>

<p >The "selectors" parameter is used to request specific packets, in effect limiting the default action of sending all data. A packet is sent to the client if it matches any positive selector (without leading "!") and doesn't match any negative selectors (with "!").  The general format of selectors is LLSSS.T, where LL is location, SSS is channel, and T is type (one of DECOTL for Data, Event, Calibration, Blockette, Timing, and Log records).  "LL", ".T", and "LLSSS." can be omitted, meaning "any".  It is also possible to use "?" in place of L and S.</p>

<pre >

Some examples:
BH?            - BHZ, BHN, BHE (all record types)
00BH?.D        - BHZ, BHN, BHE with location code '00' (data records)
BH? !E         - BHZ, BHN, BHE (excluding detection records)
BH? E          - BHZ, BHN, BHE & detection records of all channels
!LCQ !LEP      - exclude LCQ and LEP channels
!L !T          - exclude log and timing records
</pre>

## <a id='stream-list-file'>Stream List File</a>

<p >The stream list file used with the '-l' option is expected to define a data stream on each line.  The format of each line is:</p>

<pre >
<net> <station> [selectors]
</pre>

<p >The selectors are optional.  If default selectors are also specified (with the '-s' option), they they will be used when no selectors are specified for a given stream.  An example file follows:</p>

<pre >
----  Begin example file -----
# Comment lines begin with a '#' or '*'
# Example stream list file for use with the -l argument of slclient or
# with the sl_read_streamlist() libslink function.
GE ISP  BH?.D
NL HGN
MN AQU  BH? HH?
II *    BHZ
----  End example file -----
</pre>

## <a id='caveats'>Caveats</a>

<p >After receiving a data packet from the SeedLink server slink2dali will forward the Mini-SEED record to the DataLink server.  If the connection to the DataLink server is broken slink2dali will continuously try to re-connect, if the program is terminated before the record is forwarded the packet will be lost because the statefile will be written as if the record were sucessfully forwarded.  The potential, while quite small, can be minimized by running slink2dali on the same host as the DataLink server.</p>

## <a id='author'>Author</a>

<pre >
Chad Trabant
EarthScope Data Services
</pre>


(man page 2022/01/13)
