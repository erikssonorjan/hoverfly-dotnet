# hoverfly-dotnet
A .Net Library for Hoverfly (http://hoverfly.io/)

Hoverfly is maintained by SpectoLabs (https://specto.io/)

# What is Hoverfly DotNet?
Hoverfly is a lightweight service virtualisation tool which allows you to stub / simulate HTTP(S) services. It is a proxy written in Go which responds to HTTP(S) requests with stored responses, pretending to be it’s real counterpart.

It enables you to get around common testing problems caused by external dependencies, such as non-deterministic data, flakiness, not yet implemented API’s, licensing fees, slow tests and more.

Hoverfly .Net is a native language binding which gives you an expressive API for managing Hoverfly in .Net. It gives you a Hoverfly class which abstracts away the binary and API calls.

This project doesn't include the hoverfly.exe
You can download it from http://hoverfly.io/#download

###Example of using simulation:

```
var hoverfly = new Hoverfly(HoverflyMode.SIMULATE);

hoverfly.Start();

var result = //Use HttpClient to get content, e.g. "http://time.jsontest.com";

// This call will not reach the destination URL, insted Hoverfly will return a first call that is recorded.
var result2 = //Use HttpClient to get content, e.g. "http://time.jsontest.com";

hoverfly.Stop();
```

###Capture and export simulations:

```
var hoverfly = new Hoverfly(HoverflyMode.CAPTURE);

hoverfly.Start();

use for example HttpClient to make a call to a URL, e.g. "http://echo.jsontest.com/key/value/one/two");

hoverfly.ExportSimulation("simulation.json");

hoverfly.Stop();
```

###Import recorded simulations into hoverfly:

```
var hoverfly = new Hoverfly(HoverflyMode.SIMULATE);

hoverfly.Start();

hoverfly.ImportSimulation(new FileSimulationSource("simulation.json"));

// use for example HttpClient to make a call to a URL, e.g. "http://echo.jsontest.com/key/value/one/two");
// and it will return the result from the imported recorded simulation

hoverfly.Stop();
```

###Specify you own simulations for a call to specific URL:

```
var hoverfly = new Hoverfly(HoverflyMode.SIMULATE);

hoverfly.Start();

var simulation = new Simulation(
                     new HoverflyData(
                         new List<RequestResponsePair> {
                             new RequestResponsePair(
                                 new Request {
                                               Method = "GET",
                                               Scheme = "http",
                                               Destination = "echo.jsontest.com",
                                               Path = "/key/value/three/four",
                                             },
                                 new Response {
                                               Status = 200,
                                               Body = "{\n   \"three\": \"four\",\n   \"key\": \"value\"\n}\n",
                                              })}, 
                             null),
                    new HoverflyMetaData());

hoverfly.ImportSimulation(simulation);

// Every call (using for example HttpClient) to the URL http://echo.jsontest.com/key/value/three/four will now
// return the specified Body in the simulation object. This will happen as long as hoverfly is running.
var result = <Http Get Content From "http://echo.jsontest.com/key/value/three/four">

hoverfly.Stop();
