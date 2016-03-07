# ovsdb_scripts

Open Daylight OVSDB dev/debug scripts 

Note: I wrote these scripts to help me in debugging and development tasks and have 
found them very helpful. They are certainly not bug-free or customer ready. Enjoy
and please let me know if something doesn't work.

=====================
readable_flows.py:
=====================

This script pretty prints ovs flows created by ovsdb. Features include:
1) Where possible, MACs are followed by their corresponding IP in parenthases and vice-versa
2) Tunnel ids are followed by the decimal representation used by Open Daylight
3) Counters and stats are removed so that meaningful diffs may be generated
4) Table numbers are given together with descriptive names
5) Flows are grouped together by priority in decending order

Usage:
This script must be run on the OpenStack controller since it uses the 
neutron command to map MACs and IPs.
$ sudo ovs-ofctl -OOpenFlow13 dump-flows br-int | python readable_flows.py

Sample Output:
TABLE=60 (ROUTING)
    priority=2048 
            ip,reg3=0x44c,nw_dst=192.168.0.0/24 ACTIONS=set_field:fa:16:3e:16:eb:ee(192.168.0.1)->eth_src,dec_ttl,set_field:0x44c(1100)->tun_id,goto_table:70
            ip,reg3=0x445,nw_dst=192.168.2.0/24 ACTIONS=set_field:fa:16:3e:a8:54:1d(192.168.2.1)->eth_src,dec_ttl,set_field:0x445(1093)->tun_id,goto_table:70
            ip,tun_id=0x44c(1100),nw_dst=192.168.2.0/24 ACTIONS=move:NXM_OF_ETH_SRC[0..31]->NXM_NX_REG4[],move:NXM_OF_ETH_SRC[32..47]->NXM_NX_REG5[0..15],set_field:fa:16:3e:a8:54:1d(192.168.2.1)->eth_src,dec_ttl,set_field:0x445(1093)->tun_id,goto_table:70
            ip,tun_id=0x445(1093),nw_dst=192.168.0.0/24 ACTIONS=move:NXM_OF_ETH_SRC[0..31]->NXM_NX_REG4[],move:NXM_OF_ETH_SRC[32..47]->NXM_NX_REG5[0..15],set_field:fa:16:3e:16:eb:ee(192.168.0.1)->eth_src,dec_ttl,set_field:0x44c(1100)->tun_id,goto_table:70
    priority=0 
            ACTIONS=goto_table:70

=====================
pplogs.py
=====================
This script translates those huge karaf.log lines into something like this:

processEvent exit (SouthboundEvent [
  type=BRIDGE,
  action=ADD,
  augmentationData=OvsdbBridgeAugmentation{
    getBridgeExternalIds=[
      BridgeExternalIds{
        getBridgeExternalIdKey=opendaylight-iid,
        getBridgeExternalIdValue=/network-topology:network-topology/network-topology:topology[
          network-topology:topology-id='ovsdb:1'
        ]
        /network-topology:node[
          network-topology:node-id='ovsdb://uuid/0daf4997-e596-419c-91b7-2096f7fd7a86/bridge/br-int'
        ],
        augmentations={}
      } /* BridgeExternalIds */
    ] /* getBridgeExternalIds= */,
    getBridgeName=OvsdbBridgeName [
      _value=br-int
    ],
    getBridgeOtherConfigs=[
      BridgeOtherConfigs{
        getBridgeOtherConfigKey=disable-in-band,
        getBridgeOtherConfigValue=true,
        augmentations={}
      }
    ] /* getBridgeOtherConfigs= */,
    getBridgeUuid=Uuid [
      _value=6676d801-274c-4997-b2e8-c3ace9736bda
    ],
    etc....

Usage:
$ cat interesting_log_lines | python pplogs.py
