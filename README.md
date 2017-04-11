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

/usr/local/libexec/nagios/check_bgp_neighbors_v2

-rwxr-xr-x  1 root  wheel   968B  check_bgp_neighbors_v2



On Nagios Server:

Nagios command definition:

# 'check_bgp' command definition
define command{
	command_name check_bgp
	command_line $USER1$/check_nrpe -H $HOSTADDRESS$ -c check_bgp -a '$ARG1$'
}


Nagios host/service definition:

define host{
        host_name       bgx1_loopback_bgp         ; The name we're giving to this host
        alias           bgx1_loopback_bgp         ; A longer name associated with the host
        address         123.321.123.3             ; IP address of the host
	parents		bgx1
        }

define service{
        use                             generic-service
        host_name                       bgx1_loopback_bgp
        service_description             BGP1-NB
        check_command                   check_bgp!NB-Full-Routing 400000 300000
}
                                                                    ^     ^
                                                                    ^     ^
                                                                    |     \-- Min. received prefixes threshold 
                                                                    |
								    !
                                                        Expected received prefixes
                                                        
