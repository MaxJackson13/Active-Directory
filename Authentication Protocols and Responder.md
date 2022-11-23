LM (Lan Manager)
----------------
The LM hash is Microsoft's oldest form of password storage dating back to the 1980's. LM is turned off by default since Vista/Server 2008 but can be enabled on more recent systems via a GPO.

The LM hash is generated via the following algorithm.

1. Convert lowercase to uppercase
2. Pad with NULL up to 14 characters 
3. Split into two 7-byte blocks
4. Convert each block to a DES key 
5. DES encrypt the string "KGS!@#$%" with these two keys
6. Concatenate the strings

NTLM (New Technology Lan Manager)
----
This algorithm is much simpler
`NT hash = md4(utf16-le(password))`

NetNTLMv1
---------
NetNTLMv1 and v2 are authentication protocols and not a form of password storage. The NetNTLMv1 algorithm can use either the LM hash or the NT hash, as such responses are calculated using both hashes
