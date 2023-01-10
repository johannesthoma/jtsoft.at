No self-signed certificates except when machine is in test mode.





3:24
To put machine into test mode run bcdedit /set TESTSIGNING ON  and reboot
3:24
(assuming it is a Windows Driver running on 64-bit)
3:24
Apps should install without signature…
3:25
For drivers you have to have a Microsoft-signed certificate.
3:25
You need: a third party token (like DigiCert) and a Microsoft partner account.
3:26
Then run the hardware lab kit tests on your driver, get the results signed with the DigiCert certifiate and upload them to the Microsoft partner site.
3:26
After a while you can download the signed driver (plus some more files).
3:26
Hope that helps …
