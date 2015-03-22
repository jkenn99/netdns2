# IPv6 Support #

Net\_DNS2 includes full support for IPv6. This includes support for accessing IPv6 DNS servers, forward DNS lookups (the AAAA record), and reverse DNS (PTR) support, using long or short form IPv6 IP addresses.

### IPv6 DNS Servers ###

Set in a resolv.conf file, or directly in the namservers array.

```

$r = new Net_DNS2_Resolver(array('nameservers' =>  array('::1')));

```

### IPv6 Reverse DNS ###

```

$response = $r->query('::1', 'PTR');

1.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.0.ip6.arpa. 86400 IN PTR localhost.

```

### IPv6 Forward DNS (AAAA) ###

```

$response = $r->query('a2.test.com', 'AAAA');

a2.test.com. 86400 IN AAAA ff01:0:0:0:0:0:0:43

```