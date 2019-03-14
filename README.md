Another example on how to connect to the Phoenix Contact AXC F 2152, this time using C# and the UA-.NETStandard library by the OPCFoundation. https://github.com/OPCFoundation/UA-.NETStandard

Hopefully this is useful for someone!

Code is based on this example:
https://github.com/OPCFoundation/UA-.NETStandard/tree/master/SampleApplications/Samples/NetCoreConsoleClient
Almost no changes are necessary, except the creation of the UserIdentity at line 214, because the controller wants to authenticate the user by username/password. 

How to get this running:
* Leave all the OPC UA settings at default
* Check out this code (https://github.com/dschoorisse/)
* Change the IP address (variable endpointURL on line 96)
* Change credentials (UserIdentity constructor on line 214)
* Build the code (but do not run it)
* Start a power shell or command prompt and change directories to the compiled .dll

If you run it you will get a certificate error:
```
PS C:\Users\d.vanschoorisse\source\repos\ConsoleApp1\ConsoleApp1\bin\Debug\netcoreapp2.1> dotnet .\ConsoleApp1.dll
.Net Core OPC UA Console Client sample
1 - Create an Application Configuration.
2 - Discover endpoints of opc.tcp://axc-f-2152-1:4840/.
    Selected endpoint uses: Basic256Sha256
3 - Create a session with OPC UA server.
Rejected Certificate: CN=eUAServer@axc-f-2152-1
Exception: One or more errors occurred. (Certificate is not trusted.
SubjectName: CN=eUAServer@axc-f-2152-1
IssuerName: CN=eUAServer@axc-f-2152-1)
PS C:\Users\d.vanschoorisse\source\repos\ConsoleApp1\ConsoleApp1\bin\Debug\netcoreapp2.1>
```
The Opc.Ua library checks the self generate certificate the AXC F 2152 provides. This is not yet in the 'trusted' key store, so it will throw an error. 

For testing purposes you can run the program using the ```-a``` switch; it will now add every server certificate automatically to the trusted certificate directory. 

Alternatively, you can do this by yourself, navigate to the PKI folder (in my case, Windows 10 professional and .Net Core 2, located at C:\Users\username\AppData\Local\OPC Foundation\pki) and move the certificate from the AXC device from the rejected folder to the trusted directory. 

![alt text](https://raw.githubusercontent.com/dschoorisse/PLCnext-OPC-UA-.NET-minimal/master/opcua_certificates.png "Certificate storage")

From now on the extra argument isn't necessary anymore. 

Now the application will run: 

```
PS C:\Users\d.vanschoorisse\source\repos\ConsoleApp1\ConsoleApp1\bin\Debug\netcoreapp2.1> dotnet .\ConsoleApp1.dll
.Net Core OPC UA Console Client sample
1 - Create an Application Configuration.
2 - Discover endpoints of opc.tcp://axc-f-2152-1:4840/.
    Selected endpoint uses: Basic256Sha256
3 - Create a session with OPC UA server.
Rejected Certificate: CN=eUAServer@axc-f-2152-1
Exception: One or more errors occurred. (Certificate is not trusted.
SubjectName: CN=eUAServer@axc-f-2152-1
IssuerName: CN=eUAServer@axc-f-2152-1)
PS C:\Users\d.vanschoorisse\source\repos\ConsoleApp1\ConsoleApp1\bin\Debug\netcoreapp2.1> dotnet .\ConsoleApp1.dll -a
.Net Core OPC UA Console Client sample
1 - Create an Application Configuration.
2 - Discover endpoints of opc.tcp://axc-f-2152-1:4840/.
    Selected endpoint uses: Basic256Sha256
3 - Create a session with OPC UA server.
Accepted Certificate: CN=eUAServer@axc-f-2152-1
4 - Browse the OPC UA server namespace.
 DisplayName, BrowseName, NodeClass
 Server, Server, Object
   + ServerArray, ServerArray, Variable
   + NamespaceArray, NamespaceArray, Variable
   + ServerStatus, ServerStatus, Variable
   + ServiceLevel, ServiceLevel, Variable
   + Namespaces, Namespaces, Object
   + Auditing, Auditing, Variable
   + ServerCapabilities, ServerCapabilities, Object
   + ServerConfiguration, ServerConfiguration, Object
   + ServerDiagnostics, ServerDiagnostics, Object
   + VendorServerInfo, VendorServerInfo, Object
   + ServerRedundancy, ServerRedundancy, Object
   + GetMonitoredItems, GetMonitoredItems, Method
   + ResendData, ResendData, Method
   + RequestServerStateChange, RequestServerStateChange, Method
   + Alarms, 6:Alarms, Object
   + Trace, 1:Trace, Object
 DeviceSet, 2:DeviceSet, Object
   + AXC F 2152, 4:AXC F 2152, Object
 PLCnext, 5:PLCnext, Object
   + Arp.Plc.Eclr, 5:Arp.Plc.Eclr, Object
5 - Create a subscription with publishing interval of 1 second.
6 - Add a list of items (server current time and status) to the subscription.
7 - Add the subscription to the session.
8 - Running...Press Ctrl-C to exit...
ServerStatusCurrentTime: 1-3-2019 08:39:30, 1-3-2019 08:39:30, Good
ServerStatusCurrentTime: 1-3-2019 08:39:35, 1-3-2019 08:39:35, Good
```