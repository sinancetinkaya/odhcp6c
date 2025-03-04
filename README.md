# How to cross compile odhcp6c for OpenWRT in Linux

Download OpenWRT SDK

```
$ wget https://downloads.openwrt.org/releases/24.10.0/targets/qualcommax/ipq807x/openwrt-sdk-24.10.0-qualcommax-ipq807x_gcc-13.3.0_musl.Linux-x86_64.tar.zst
```

 Extract the SDK
```
$ tar -xf openwrt-sdk-24.10.0-qualcommax-ipq807x_gcc-13.3.0_musl.Linux-x86_64.tar.zst

$ cd openwrt-sdk-24.10.0-qualcommax-ipq807x_gcc-13.3.0_musl.Linux-x86_64/
```
Export the environment variables
```
$ export STAGING_DIR=$(pwd)/staging_dir
$ export TOOLCHAIN_PATH=$STAGING_DIR/toolchain-aarch64_cortex-a53_gcc-13.3.0_musl
$ export PATH=$TOOLCHAIN_PATH/bin:$PATH
$ export TOOLCHAIN_PREFIX=aarch64-openwrt-linux-
```
clone odhcp6c from this repo
```
$ cd ..
$ git clone https://github.com/sinancetinkaya/odhcp6c.git
```
Compile
```
$ cd odhcp6c/
$ cmake . -DCMAKE_TOOLCHAIN_FILE=./toolchain-openwrt-armv8.cmake
$ make -j$(nproc)
```
Check the odhcp6c executable
```
$ file odhcp6c
odhcp6c: ELF 64-bit LSB executable, ARM aarch64, version 1 (SYSV), dynamically linked, interpreter /lib/ld-musl-aarch64.so.1, with debug_info, not stripped
```
---
odhcp6c - Embedded DHCPv6 Client


** Abstract **

odhcp6c is a minimal DHCPv6 and RA-client for use in embedded Linux systems
especially routers. It compiles to only about 35 KB (-Os -s).


** Features **

1. IPv6 bootstrap from different environments with autodetection
	a) RA only
	b) RA + stateless DHCPv6
	c) RA + stateful DHCPv6 (either IA_NA or IA_PD or both)

2. Handling of non-temporary addresses (IA_NA)
	a) handling of valid and preferred lifetimes
	b) automatic fallback to stateless or PD-only mode

3. Support for DHCPv6 extension
	a) Reconfigure-Messages
	b) Prefix Delegation (including handling of valid and preferred lifetimes)
	c) Prefix Exclusion
	d) DNS Configuration Options
	e) NTP Options
	f) SIP Options
	g) Information-Refresh Options
	h) Configurable SOL_MAX_RT
	i) DS-Lite AFTR-Name Option
	j) Softwire address and port mapped clients (MAP, LW4over6)
	j) CER-ID (experimental)
	k) Server unicast Option

4. Support for requesting and parsing Router Advertisements
	a) parsing of prefixes, routes, MTU and RDNSS options


** Compiling **

odhcp6c uses cmake:
* To prepare a Makefile use:  "cmake ."
* To build / install use: "make" / "make install" afterwards.
* To build DEB or RPM packages use: "make package" afterwards.

** State Script **

The state script is called whenever the DHCPv6 state changes.
The script is called with the following parameters: <interface> <state>


States:
* started		The DHCPv6 client has been started
* bound			A suitable server was found and addresses or prefixes acquired		
* informed		A stateless information request returned updated information
* updated		Updated information was received from the DHCPv6 server
* ra-updated		Updated information was received from via Router Advertisement
* rebound		The DHCPv6 client switched to another server
* unbound		The DHCPv6 client lost all DHCPv6 servers and will restart
* stopped		The DHCPv6 client has been stopped


Environment:
* RDNSS			A space-separated list of recursive DNS servers
* DOMAINS		A space-separated list of DNS search domains
* SNTP_IP		A space-separated list of SNTP server IP addresses
* SNTP_FQDN		A space-separated list of SNTP server FQDNs
* SIP_IP		A space-separated list of SIP servers
* SIP_DOMAIN		A space-separated list of SIP domains
* OPTION_<num>		Custom option received as base-16
* PREFIXES		A space-separated list of prefixes currently assigned
				Format: <prefix>/<length>,preferred,valid[,excluded=<excluded-prefix>/<length>][,class=<prefix class #>]
* ADDRESSES		A space-separated list of addresses currently assigned
				Format: <address>/<length>,preferred,valid
* RA_ADDRESSES		A space-separated list of addresses from RA-prefixes
				Format: <address>/<length>,preferred,valid
* RA_ROUTES		A space-separated list of routes from the RA
				Format: <address>/<length>,gateway,valid,metric
* RA_DNS		A space-separated list of recursive DNS servers from the RA
* RA_DOMAINS		A space-separated list of DNS search domains from the RA
* RA_HOPLIMIT	Highest hop-limit received in RAs
* RA_MTU		MTU-value received in RA
* RA_REACHABLE	ND Reachability time
* RA_RETRANSMIT	ND Retransmit time
* AFTR			The DS-Lite AFTR domain name
* MAPE / MAPT / LW4O6	Softwire rules for MAPE, MAPT and LW4O6
