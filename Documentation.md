# Using Net\_DNS2 #

### Basic Query Syntax ###

```

require_once("Net_DNS2/Net/DNS2.php");

//
// create new resolver object, passing in an array of name
// servers to use for lookups
//
$r = new Net_DNS2_Resolver(array('nameservers' => array('192.168.0.1')));

//
// execute the query request for the google.com MX servers
//
try {
	$result = $r->query('google.com', 'MX');

} catch(InvalidArgumentException $e) {

	echo "Failed to query: ", $e->getMessage(), "\n";
}

//
// loop through the answer, printing out the MX servers retured.
//
foreach($result->answer as $mxrr)
{
	printf("preference=%d, host=%s\n", $mxrr->preference, $mxrr->exchange);
}

```

### Basic Update Syntax ###

```

require_once("Net_DNS2/Net/DNS2.php");

//
// create a new Updater object
//
$u = new Net_DNS2_Updater('example.com', array('nameservers' => array('192.168.0.1')));

//
// create a new MX RR object to add to the example.com zone
//
$mx = Net_DNS2_RR::fromString('test.example.com MX 10 mail.google.com');

//
// add the record
//
$u->add($mx);

//
// add a TKEY to authentication the request
//
$u->signature('my-key', '9dnf93asdf39fs');

//
// execute the request
//
try {
	$u->update();

} catch(InvalidArgumentException $e) {

	echo "Failed to update: ", $e->getMessage(), "\n";
}

```