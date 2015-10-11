# OSMS (Open, Secure Messaging Service)
A secure-by-design messaging service inspired by Adam Caudill's SMIMP specification

### Special Thanks
Thanks to everyone who helped with the SMIMP specification!

* Adam Caudill
* Taylor Hornby
* Brandon Wilson
* Marsh Ray

### Introduction
OSMS is designed from the SMIMP specification with my own changes and additions. While many changes are required in order to retrofit SMIMP for use in a "closed-loop" setup, other changes are simply my own additions. Please open an issue with suggestions or criticisms!

### Identity Management
The same approach will be taken as from SMIMP, except rather than transport data as JSON documents it will be transported as Gobs, which have been chosen for ease in communicating in a Go-specific environment.

### Messaging
Messages will be securely encrypted using OTR, with enforced authentication. All user data on the server will be downloadable, including public keys.

### Specification
The OSMS specification is modeled after the SMIMP specification. When an approach is taken from the SMIMP specification, it will be noted. Anything else can be assumed to be a change from SMIMP.

### Concepts & Notes
#### HTTPS & Tor
Rather than follow SMIMP's perscription of HTTPS (with certificate pinning), OSMS will strictly operate as a Tor Hidden Service. HTTPS will be considered if OSMS scales to the point where the Tor process is no longer on the same machine as the webservice.
#### Ephemeral & Non-Ephemeral Keys
It is up to the client to determine whether keys are ephemeral or not. It is the responsibility of the client to revoke and replace any keys it deems ephemeral. It is suggested that a key is published on the server at all times so communications can be established, but it is by no means necessary.
#### Usernames
Usernames will be alphanumeric. The client may request a change in username at any time.
### Public Data (Unauthenticated)
#### Get User Information
The client can request all user information held on the server about a user by GETing `/user/{username}`. Fortunately, the only user data that will be kept is the username itself and one public key.
Rather than track user history through a chain of signed hashes as described in the SMIMP specification, OSMS aims to allow the public to audit user history by allowing unauthenticated access to this data for all users. The data will be returned as a Gob.
### Session Management
#### Create Session
To login, an OTR conversation must be established between the client and the server. In order to establish the converstation, a GET request must first be made to `/pk` to retrieve the latest public key for the server. Beginning with a query message, the conversation can be started by POSTing to `/create_session` until the handshake is complete and a secure OTR conversation has been established. The client public key for the conversation must match the one stored on the server.
#### Destroy Session
To logout, the established OTR conversation must be ended by POSTing a termination message to `/terminate_session`.

