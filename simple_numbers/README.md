1. За минуту гугления находим https://github.com/Ganapati/RsaCtfTool.git
```bash
ctf@ctf:~/simple_numbers/RsaCtfTool-master$ for i in {1..5}; do python2 ./RsaCtfTool.py --publickey ../public.pem --uncipherfile ../$i.crypt.64;done
[+] Clear text : WGCTF{1E
[+] Clear text : 6DE23691
[+] Clear text : 2CA43412
[+] Clear text : 29C7FAEE
[+] Clear text : A1D121}
```