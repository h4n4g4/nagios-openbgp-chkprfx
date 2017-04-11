# nagios-openbgp-chkprfx
Nagios NPRE plugin to check received prefixes from OpenBGPD IPv4/IPv6 sessions

Nagios NRPE nrpe is used to execute Nagios plugins on remote hosts and report the results to the main Nagios server. From the Nagios homepage: Allows you to execute "local" plugins (like check_disk, check_procs, etc.) on remote hosts. The check_nrpe plugin is called from Nagios and actually makes the plugin requests to the remote host. Requires that nrpe be running on the remote host (either as a standalone daemon or as a service under inetd). 

Requirements:

- FreeBSD / pfSense with OpenBGPD;
- NRPEv2 (https://github.com/pfsense/FreeBSD-ports/commits/devel/net-mgmt/pfSense-pkg-nrpe).

- Nagios Monitoring server.

Usage:

Files

On FreeBSD box with NRPEv2:

-rwxr-xr-x  1 root  wheel   968B Dec  2 23:23 check_bgp_neighbors_v2

#! /bin/sh
#states
STATE_OK=0
STATE_WARNING=1
STATE_CRITICAL=2
STATE_UNKNOWN=3

NEIGHBOR=$1
WARNING=$2
CRITICAL=$3


if [ -e $NEIGHBOR ] || [ -e $WARNING ] || [ -e $CRITICAL ];
then
        echo "Missing parameters"
	exit 1
elif [ -n "$4" ]; then 
	echo "Extra parameters"
	exit 1
fi

NEIGHBOR_STATE=$(bgpctl sh nei $NEIGHBOR | grep "BGP state" | awk '{print $4}' | sed 's/,//')
PREFIXES_RECEIVED=$(bgpctl sh | grep "$NEIGHBOR " | awk '{print $7}' | sed 's/\/.*//')

VALID_PREFIXES=$(echo $PREFIXES_RECEIVED | grep "^-\?[0-9]*$")



if [ $NEIGHBOR_STATE == "Established" ];
then
        echo "$NEIGHBOR_STATE: $PREFIXES_RECEIVED prefixes received"
else
		return $STATE_CRITICAL
fi

if [ -z "$VALID_PREFIXES" ]; 
#if [ -z "$PREFIXES_RECEIVED" ];
then	
		return $STATE_CRITICAL
elif [ "$PREFIXES_RECEIVED" -lt "$CRITICAL" ];
then
        return $STATE_CRITICAL
elif [ "$PREFIXES_RECEIVED" -lt "$WARNING" ];
then
        return $STATE_WARNING
else
		return $STATE_OK
fi








Nagios service definition:

define service{
        use                             generic-service
        host_name                       bgx1_loopback_bgp
        service_description             BGP1-PTT-RJ_RS4
        check_command                   check_bgp!PTT-SP-RS4 14000 10000
}

                                                               ^     ^
                                                               |     \-- Min. prefixes 
                                                               |
                                                         Max. prefixes
                                                        
