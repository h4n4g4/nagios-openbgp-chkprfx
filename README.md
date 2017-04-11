# nagios-openbgp-chkprfx
Nagios NPRE plugin to check received prefixes from OpenBGPD IPv4/IPv6 sessions

Nagios NRPE nrpe is used to execute Nagios plugins on remote hosts and report the results to the main Nagios server. From the Nagios homepage: Allows you to execute "local" plugins (like check_disk, check_procs, etc.) on remote hosts. The check_nrpe plugin is called from Nagios and actually makes the plugin requests to the remote host. Requires that nrpe be running on the remote host (either as a standalone daemon or as a service under inetd). 

Requirements:

On router box:

- FreeBSD / pfSense with OpenBGPD;
- NRPEv2 (https://github.com/pfsense/FreeBSD-ports/commits/devel/net-mgmt/pfSense-pkg-nrpe).


- Nagios Monitoring server.


                                                        
