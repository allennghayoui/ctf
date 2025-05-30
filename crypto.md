# Cryptography

## Breaking RSA with RsaCtfTool
### Decrypt Ciphertext with N, E, and Ciphertext Available
`docker run -it --rm $PWD:/data rsactftool/rsactftool -n <N> -e <E> --decrypt <ciphertext> --private`
This will only work if P and Q were not chosen as large enough prime numbers. Check [factordb.com](https://factordb.com) to check in case the tool does not work.
### Try Recovering Private Key from Public Key
`docker run -it --rm $PWD:/data rsactftool/rsactftool --publickey <public-key-file> --private`
**For more options check [github/rsactftool](https://github.com/RsaCtfTool/RsaCtfTool)**


## Hash Functions
### Reversing MD5 and SHA-1 Hash Functions
Use [crackstation.net](https://crackstation.net/)
### Brute Forcing Hashes with `hashcat`
`hashcat -a 0 -m <hash-type-code> /usr/share/wordlists/rockyou.txt`
Common hash type codes:
	- 0 = MD5
	- 100 = SHA-1
	- 900 = MD4
	- 1000 = NTLM
	- 1400 = SHA256
	- 1700 = SHA512
	- 5600 = NetNTLMv2
**For more hash modes use `man hashcat` and press spacebar 12 times until you see hash types**
