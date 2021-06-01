# ddos-protection
a collection of stuff about DDoS Protection

## I want to help

Do you have tips or packet captures (pcap, pcapng, sflow) that you are willing to share? Please open up a github issue / PR and share them.


### L7 HTTP floods

The easiest way is to use cloudflare, there are also some nice webservers over here on github.

Using NGINX with php-fpm? It can help a lot to turn up the php-fpm thread in the php-fpm.conf. This will allow the system to use all resources.


### UDP Volumetric
These are hard to filter and the most common against gameservers. If you don't need UDP for anything you can turn it off, often you will need to specifically whitelist the IP of your webserver though. 
Make sure you don't just blanket allow port 53 as this is often used for DNS reflection attacks.

#### Popular udp attack ports
Excerpt from my filter script, might be wrong
```c
			udphdr->dest == bpf_htons(100) ||  //random
			udphdr->dest == bpf_htons(161) ||  //snmp
			udphdr->dest == bpf_htons(3702) || //soap over UDP random data
			udphdr->dest == bpf_htons(5353) || //multicast DNS
			udphdr->dest == bpf_htons(427) ||  //PORTMAP / SRVLOC
			udphdr->dest == bpf_htons(5060) || //SIP
			udphdr->dest == bpf_htons(5683) || //CoAP
			udphdr->dest == bpf_htons(520) ||  // RIP
			udphdr->dest == bpf_htons(500) || // ISAKMP
			udphdr->dest == bpf_htons(177) || //XDMCP

			//UNTESTED
			udphdr->dest == bpf_htons(3283) || // ARD/ARMS (apple remote desktop & management)
			udphdr->dest == bpf_htons(3702) || //WS-Disvoery / WS-DD  ( Web Services Dynamic Discovery (WS-Discovery))
			//END UNTESTED

			udphdr->dest == bpf_htons(137) ||
			udphdr->dest == bpf_htons(138) || //NETBIOS NBDS
			udphdr->dest == bpf_htons(139) ||

			udphdr->source == bpf_htons(111) || //PORTMAP
			udphdr->source == bpf_htons(389) /* (C)LDAP */ ||
			udphdr->source == bpf_htons(11211) /*Memcached */ ||
			udphdr->source == bpf_htons(5060) /* SIP */ ||
			
			udphdr->source == bpf_htons(5351) /* NAT-PMP */ ||
			udphdr->source == bpf_htons(19) /* CHARGEN, source or dest? */ ||
			udphdr->source == bpf_htons(7) /* ECHO, source or dest? */ ||
			udphdr->source == bpf_htons(17) /* QUOTD source or dest? */ ||
			udphdr->source == bpf_htons(1900) /* SSDP*/ ||
```
udphdr->dest is the destination port on your machine, udphdr->soure is the source of the ddos attack.

Keep in mind the source ips of these attacks are often legit machines. They are only being abused as a reflector for a botnet.


### SYN flood
implement syn cookies, preferably on a very fast machine with XDP or a specialized firewall. For large scale attacks look at implementing p0f signature checks.

## Interesting opensource projects
https://github.com/pavel-odintsov/fastnetmon ddos detection from sFlow.

https://github.com/jjsantanna/pcap_dns_ddos_analysis packet capture analysis for automation and better understanding a attack


XDP is also a very promising technology for ddos protection:

https://www.mcorbin.fr/pages/xdp-introduction/ introduction article to XDP

https://github.com/xdp-project/xdp-tutorial The holy grail of XDP information. This repository contains a lot of getting-started information for packet handling in the kernel.

https://github.com/PlushBeaver/xdp-syn-cookie syn cookies implemented in XDP space. About 50% faster when runnning in SKB mode than the normal kernel system and toggleable on demand.

https://github.com/matthewbentley/ebpf-flowradar WIP flowradar implementation in XDP using bloom filters


More research:
https://github.com/Phenomite/AMP-Research UDP amplification ports

https://gitlab.com/cryptio/ddos-research/-/tree/master chase's anti ddos research.

### Other
// TODO, want to contribute? Please open up a PR or github issue.
