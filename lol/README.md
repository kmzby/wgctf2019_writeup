1. Открываем дамп с помощью Wireshark, видим кучу TLS пакетов и 
расстраиваемся. Поиском по строкам слов LOGIN,AUTH и т.п. находим 
cleartext отсылку письма по SMTP. 
Follow TCP stream, выдергиваем из письма RSA ключ и картинку в base64.
Картинку пока откладываем на будущее.
 
2. Пытаемся применить ключ к SSH, неуспешно. Возвращаемся к Wireshark, 
обращаем внимание, что TLS версии 1.2; ищем как расшифровывать и находим 
способ с помощью приватного ключа. Подставляем наш ключ и находим логин 
и пароль к админке ```username=admin&password=gaiquaiW4deigh7Fee2ohj8queuyopohngai5Laey1peishiguexi8ii7fiejookahghequi7upiufiebooXahneiki5oeph1ma2```.
 
3. Быстро находим в админке RCE, ```8.8.8.8;cat flag``` отдает нам флаг `WGCTF{FD52334CF8E55D0DF85C34D20AE3D32B}`
 
4. Там же проверяем /etc/passwd и получаем первый доп.флаг

5. Возвращаемся к картинке. ```less matrix.jpg``` и в конце файла искомое `file.txtWGCTF{C3FC2E6E3273217179A08843BE2F6E57}`