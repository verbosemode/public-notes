
# SNMP OIDs for monitoring Ceragon Fibeair IP-10 equipment

Icinga 1 / Nagios compatible check command definitions

* SNMPV2_COMMUNITY should be defined in resources.cfg (better, use SNMPv3)
* SLOT_NUMBER To query a different slot via SNMP, the community format has to like like this COMMUNITY_SLOTNUMBER

	check_ceragon_acm_profile_slot{{SLOT_NUMBER}}
	check_snmp!-P 2c -C {{SNMPV2_COMMUNITY}}_{{SLOT_NUMBER}}!-l "RX" -o 1.3.6.1.4.1.2281.10.7.4.1.1.9.1,1.3.6.1.4.1.2281.10.7.4.1.1.5.1 -l "TX" -l "ACM Profile"
	 
	check_ceragon_idu_temp
	check_snmp!-P 2c -C {{SNMPV2_COMMUNITY}}!-o 1.3.6.1.4.1.2281.10.1.1.9.0 -l "IDU Temp" -u "C" -w 65 -c 70
	 
	check_ceragon_odu_temp
	check_snmp!-P 2c -C {{SNMPV2_COMMUNITY}}!-o 1.3.6.1.4.1.2281.10.5.1.1.4.1 -l "ODU Temp" -u "C" -w 65 -c 70
	 
	check_ceragon_rx-level_slot{{SLOT_NUMBER}}
	check_snmp!-P 2c -C {{SNMPV2_COMMUNITY}}_{{SLOT_NUMBER}}!-o 1.3.6.1.4.1.2281.10.7.3.1.1.4.1 -l "Rx level" -u "dBm" -w 60 -c 65
	 
	check_ceragon_tx-level_slot{{SLOT_NUMBER}}
	check_snmp!-P 2c -C {{SNMPV2_COMMUNITY}}_{{SLOT_NUMBER}}!-o 1.3.6.1.4.1.2281.10.5.1.1.3.1 -l "Tx level" -w 60 -c 65
	 
	check_ceragon_xpi_slot{{SLOT_NUMBER}}
	check_snmp!-P 2c -C {{SNMPV2_COMMUNITY}}_{{SLOT_NUMBER}}!-o 1.3.6.1.4.1.2281.10.7.1.1.5.1 -l "XPI"
