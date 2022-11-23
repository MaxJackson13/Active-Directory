Key Terms
---------
- KDC: Key Distribution Centre
- TGT: Ticket Granting Ticket
- AS: Authentication Server
- TGS: Ticket Granting Server
- AS-REQ: AS-Request
- AS-REP: AS-Reply
- PRE-AUTH: Pre-Authentication

Kerberos Pre Authentication 
---------------------------
In kerberos PRE-AUTH, a user encrypts a timestamp sent by the KDC with their NT password hash. They send this to the KDC who attempts to decrypt it (the KDC has a record of all password hashes in the domain). If the timestamp decrypts to within a few minutes of the timestamp the KDC sent the user, the KDC trusts the user is who they say they are. They can then proceed to request a TGT (AS-REQ)

How Can We (Ab)use This
-----------------------
However if PRE-AUTH is disabled for an account- which may be the reason for backwards compatibility- an attacker can forge an AS-REQ and receive a TGT. So if an attacker can enumerate users with PRE-AUTH disabled (i.e through LDAP though this will usually require a successful LDAP bind which is limited to authenticated users already), or if they have a list of known usernames, they may be able to brute-force an AS-REP by requesting a TGT for each user, and if PRE-AUTH is disabled for any user, they'll receive a AS-REP from the KDC for that user. 

Credential Time
---------------
While an AS-REP contains the TGT which is encrypted with the KDC long-term key (the krbtgt password NT hash) so is therefore uncrackable, an AS-REP also returns some material encrypted with the client long-term key- the client's password NT hash. This encrypted material contains a session key, which alone we could not use to derive the client's password as we don't know what the key is so would never know when we have decrypted it correctly, however among this material is some known plaintext such as the realm and user principal name. As a result we can guess a password, calculate the NT hash (which is just `md4(utf16-le(password))`, decrypt the encrypted material and see if the known realm and user principal name are among the decrypted text. If they are we very likely have the correct password. One thing to note is that while Windows environemnts default to AES256 encryption, since we are forging an AS-REQ, we can specify our preferred encryption type, allowing us to downgrade the encryption to something much more manageable, in this case RC4-HMAC
