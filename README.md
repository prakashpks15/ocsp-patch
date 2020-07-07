# ocsp-patch

Curl implementation to support ocsp method. Default curl does support only ocsp stapling.
This patch provides:
1. Certificate verification by contacting CA using ocsp method.

Implement utility to send ocsp request directly to CA in absence of ocsp response from TLS handshake.
Steps to implement:
1. Initiate client hello TLS handshake by stapling ocsp status request.
2. If client receives stapled ocsp response as part of initial handshake, then do the following
2.1 cert status GOOD: continue establish the connection.
2.2 cert status REVOKED: terminate TLS connection immediately.
2.3 cert status UNKNOWN: terminate TLS connection immediately.
3. If client didn't receives stapled ocsp response as part of initial handshake, then
3.1 Get and parse the server certificate and store it in .PEM format.
3.2 Get, parse the CA's chain of certificates and store it in .PEM format.
3.3 parse the OCSP responder URI from the server certificate.
3.4 Prepare ocsp request to send to CA's responder.
3.5 Get the OCSP response from CA's responder.
3.6 Once we get the ocsp response, repeat 2.
