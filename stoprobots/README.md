1. немного повводили значения руками чтобы понять схему формирования последовательности 
```python
import httplib2
from BeautifulSoup import BeautifulSoup
from urllib import urlencode

http = httplib2.Http()
headers = {'Cookie': 'PHPSESSID=28cad23aba981df7a576a5b85d08631e', 'Host': 'stoprobots.ctf2019.rocks', 'Content-type': 'application/x-www-form-urlencoded'}
dictionary = "1234567890ABCDEFGHIJKLMNOPQRSTUVWXYZ" * 200
len_d = len(dictionary)
while True:
    resp, c = http.request("http://92.223.25.122", "GET", headers=headers)
    bs = BeautifulSoup(c)
    round = int(str(bs.h3).split(" ")[3].split("<")[0])
    s_pos = round -1

    len = round % 10
    if len < 4 :
        len = 3
    e_pos = s_pos + len
    data = {'data': dictionary[s_pos:e_pos]}
    print round, data
    resp, c = http.request("http://92.223.25.122/index.php", method="POST", body=urlencode(data), headers=headers)
```
на 2000 итерации выплюнуло основной флаг 

2. В стеганограмме картинки с полследней итерации был второй флаг 
3. nmap показал остатки git репозитория, стянув которые получили 3-й флаг

