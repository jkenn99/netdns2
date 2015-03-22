# Change Log #

### Version 1.3.2 ###

  * added support for the EUI48 and EUI64 resource records (RFC7043)
  * fixed how we handle the return values from socket select() statements; this wasn't causing a problem, but it wasn't quite right
  * added some error messaging when the socket times out
  * before we cache the data, unset the rdata value; this was causing some JSON errors to be generated, and we don't need the data anyway.

### Version 1.3.1 ###

  * added the Net\_DNS2\_Packet\_Request and Net\_DNS2\_Packet\_Response objects to the Net\_DNS2\_Exception object
  * added support in the TSIG class for SHA algorithms (requires the hash extension, which is included in PHP >= 5.1.2), patch provided by Manuel Mausz
  * added support for the NID, L32, L64, and LP DNS RR's (RFC6742)
  * lots of phpcs cleanup

### Version 1.3.0 ###

  * re-worked a lot of the code around OPT RR's, including adding support for the DO flag
  * added the AD and CD flags to the Net\_DNS2\_Header class for DNSSEC
  * added a new function to keep track of RR's that should NOT be cached.
  * added a new flag (dnssec) to request DNSSEC lookups; this adds an OPT RR to the additional section
  * added a new flag (dnssec\_payload\_size) to adjust the EDNS(0) UDP payload size.
  * added a new flag (dnssec\_cd\_flag) to set the DNSSEC CD bit to disable signature validation.
  * added a new flag (dnssec\_ad\_flag) to set the DNSSEC AD bit to request authentic data without needing to set the DO flag.
  * fixed an issue in Net\_DNS2\_Socket\_Sockets; Windows (specifically < 2003) has problems with MSG\_WAITALL
  * changed Net\_DNS2\_RR::construct() to use get\_class() instead of get\_called\_class()
  * added a DNSSEC test to the testing suite.

### Version 1.2.5 ###

  * changed the socket\_connect() code to start off non-blocking, and call select() after connect() so a timeout on a invalid server works properly
  * added the new TLSA RR - RFC 6698
  * fixed the socket defines again; apparently the values of the SOCK are different under solaris
  * changed the Net\_DNS2\_Updater::update() so you can pass a reference to a variable that will be populated with the response object
  * moved the lines that add the response server/type to after the is\_null() check- it should have been there to begin with.
  * fixed a whole bunch of cases where I wasn't incrementing the offset values properly
  * added support to set the RD (recursion desired) bit when making a request

### Version 1.2.4 ###

  * added the name server that the response came from to the Net\_DNS2\_Packet\_Response object.
  * added the socket type (TCP or UDP) that was used for the request.
  * changed the internal socket logic to use a local define for SOCK\_STREAM and SOCK\_DGRAM rather than the one provided with the sockets library; this isn't available if you're not using the library.
  * fixed a bug when an AXFR fails; rather than returning right away, it was waiting until there was a timeout condition. Now it returns right away on failure.

### Version 1.2.3 ###

  * added a LICENSE file to satisfy fedora packaging guidelines.
  * included the tests folder in the PEAR package.
  * updated the tests to not include a specific include\_path, and to use the Google public DNS servers for the resolution tests, rather than the /etc/resolv.conf file (which won't exist on a Windows system)
  * fixed a endless loop bug in the Streams.php file; this was related to the code changes I made + UDP packets.

### Version 1.2.2 ###

  * added some trimming of whitespace to Net\_DNS2\_RR::buildString(); we'd get some uninitialized string offset errors if there was some blank space at the end.
  * fixed a few cases where Net\_DNS2\_Lookup where it should Net\_DNS2\_Lookups.
  * added support for a strict query mode, that lets you handle the weird way DNS handles failed lookups + CNAME records; see RFC 1034 section 3.6.2 for more information.
  * fixed some warning messages that were coming from the cache classes when a json\_decode() would fail.
  * fixed a bug in Net\_DNS2\_Cache\_File and Net\_DNS2\_Cache\_Shm; it would try to write the file even if caching was turned off
  * made sure we don't cache records when we do a zone transfer.
  * added some blocking in both the Sockets.php and Streams.php file around the read function.
  * I wasn't handling multi-message zone transfers properly; now we loop through and read all the messages and pack them together as one big result

### Version 1.2.1 ###

  * changed the Net\_DNS2\_Sockets::sock property from private to protected; this was causing some problems when the request was failing.
  * PHP doesn't support unsigned integers, but many of the RR's return unsigned values (like SOA), so there is the possibility that the value will overrun on 32bit systems, and you'll end up with a negative value. So a new function was added to convert the negative value to a string with the correct unsigned value.

### Version 1.2.0 ###

**This version changes the way some exceptions are thrown, and may break your code!**

  * added numeric error codes to the Lookups class, and had each method that throws an exception throw a numeric error code along with the message.
  * dropped all references to InvalidArgumentException- we only use the Net\_DNS2\_Exception from now on.
  * added the CAA, URI, TALINK, CDS and TA resource records. Some of these are experimental, but are pretty straight forward.
  * fixed a bug in formatString(); my version was only putting double quotes around strings that have spaces, but apparently ALL strings should have double quotes around them. This is how BIND does it.
  * re-organized the Net\_DNS2\_Lookups initialization code; it no longer creates a global object of itself.
  * fixed a bug in the caching code; in some cases it wouldn't cache the same content more then once.
  * added an option to use JSON to serialize the cache data rather than using the PHP serialize function. JSON is much faster, but loses the class definition, and becomes a stdClass object.
  * fixed a handful of cases where I was using double quotes (") where a single quote (') would be fine.

### Version 1.1.4 ###

  * fixed a math issue with the name compress logic; this would somtimes screw up Updates with multiple RR's
  * added a new test in the test suite, that validates that the domain name compression and expansion works properly

### Version 1.1.3 ###

  * added support for IPv6 DNS servers; from resolv.conf files, and from name server arrays
  * added new IPv4 and IPv6 validation functions, and a IPv6 expand function
  * changed the A, AAAA and IPSECKEY RR&apos;s to use the new validation methods
  * added support for IPv6 PTR records
  * dropped the old IPv4 regex from the lookups class

### Version 1.1.2 ###

  * fixed a bug in the Net\_DNS2\_Updater class; I wasn't resetting the internal packet request values, so in some cases making more than one request on the same instance would fail.
  * Fixed a bug in Net\_DNS2; I wasn't handling comments properly when parsing the resolv.conf file.
  * check for duplicate entries when adding/deleting entries in the Updater() class; BIND will throw and error if you try to delete the same RR twice in the same request, not sure if this is expected behaviour
  * modified several RR's to clean up the trailing period when it's displayed on hosts.

### Version 1.1.1 ###

  * fixed a bug in the autoload function; if you had another autoload function defined, and tried to load a class with an underscore in the name, it would fail.

### Version 1.1.0 ###

  * added support for signing requests using SIG(0); uses the PHP openssl extension, and only currently supports RSA- DSA support in PHP is limited.
  * moved the signing code for SIG(0) and TSIG to the base class so it can be used for both updates and queries (for AXFR)
  * added a simple local cache feature using shared memory (shmop extension) or using a flat fille. This feature is disabled by default.
  * added support for the ATMA resource record (supported in Windows)