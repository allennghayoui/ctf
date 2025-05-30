# General Tips

## General CTF Resources
- [CyberChef](https://gchq.github.io/CyberChef/): General operations.
- [dencode](https://dencode.com/): Multiple Encoding/Decoding algorithms.
- [revshells](https://www.revshells.com/): Easily get code for reverse shell with different options for platforms and languages to choose from.
- [Practical CTF](https://book.jorianwoltjer.com/): Explanation and example scripts for common CTF challenges.
- [Cyber Training Guide](https://cyber.coleellis.com/): CTF writeups grouped by category that can help if you know what kind of problem you're facing.
- [CTF Cheat Sheet](https://dvd848.github.io/CTFs/CheatSheet.html): Common commands, tools, and techniques for CTF challenges grouped by category.
- [Common CTF Challenges Resources](https://github.com/JohnHammond/ctf-katana): Contains list of helpful tools for CTF challenges based on category.

## Web Resources:
- [CSRF Generator](https://csrfshark.github.io/app/): Paste the request copied from Burpsuite repeater and paste it to generate HTML code for CSRF exploit.
- [GraphQL Visualizer](https://graphql-kit.com/graphql-voyager/): Visualizes relationships in GraphQL schema by pasting result from introspection query.
- [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master): Web payloads for different vulnerabilities (SQLi/NoSQLi, CSRF, XSS, Command injection, SSRF, ...).
- [PayloadBox](https://github.com/orgs/payloadbox/repositories): More payloads.
- [Jinja Payloads](https://www.onsecurity.io/blog/server-side-template-injection-with-jinja2/): Payloads to bypass filters and get remote code execution on websites using Jinja templating engine.

## Crypto Resources:
- [dcode](https://www.dcode.fr/en): Decrypter for different algorithms.
- [RsaCtfTool](https://github.com/RsaCtfTool/RsaCtfTool): Tool to break weak RSA encryption.
- [Keyed Caesar Cipher](https://www.boxentriq.com/code-breaking/keyed-caesar-cipher): Caesar cipher with key decryption tool.

## OSINT Resources:
- [OSINT Framework](https://osintframework.com/): Groups of OSINT tools based on use-case.

## Useful Commands
### Recursively Search For A Word In A Directory's Files
`grep -r <word-to-find> <path-of-directory-to-search>`
### Recursively Search For A File In A Directory By Case Insensitive Name
`sudo find <path-of-directory-to-search> -type f -iname <name-of-file-to-find> 2>/dev/null`
### Recursively Search For A Directory In A Directory By Case Insensitive Name
`sudo find <path-of-directory-to-search> -type f -iname <name-of-directory-to-find> 2>/dev/null`

## Check Web Page's Source Code

## Check `/robots.txt` File

## Send A TRACE request To Endpoints For Information Disclosure

## Check For A `.git` Directory


