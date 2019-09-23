1. Первое что смутило - `index.php?page=login.php`, что очень похоже на include().
Проверили на LFI `http://otp.ctf2019.rocks/index.php?page=../../etc/passwd` n получили первый доп, ура! :)

2. Второе что вызвало подозрение - картинка, которая отрисовается при ошибке:
http://otp.ctf2019.rocks/1st_part.png
 Просто от балды попробовали имена 2nd_part.png, 3rd_part.png, 4th_part.png и тд (нашлась только вторая).
 В stegsolve наложили вторую картинку на первую xor'м и получили второй доп.

3. В http://otp.ctf2019.rocks/index.php?page=../../etc/sudoers увидели, что
 otp запускает приложение-чекалку на Erlang, скачали otp.beam. 
 Помучались с подбором версии эрланга для декомпиляции (использовали utility.erl для декомпиляции), поняли как работает
  чекалка, переписали на вывод в stdout переменной Otp. Через repl код 
  выдавал почему-то невалидный токен, пришлось компилировать код. 
  
```
┌─[16:25][p-mihadyuk][~]
└╼ cat a.erl
-module(a).

-export([genotp/0]).


genotp() ->
    inets:start(),
    crypto:start(),
    {ok, {_, _, Body}} =
        httpc:request("http://worldclockapi.com/api/json/utc/now"),
    Time = hd(tl(tl(string:tokens(hd(lists:filter(fun
                                (Val) ->
                                string:str(Val,
                                       "currentDateTime")
                                  > 0
                              end,
                              string:tokens(Body, ","))),
                      "\"")))),
    Otp =
        string:slice(base64:encode_to_string(crypto:hmac(sha256,
                                 "oow7aFoh3hoh1cichen5kahd1yeuhohpiegh8loozuHoo"
                                 "s2quucaix4aaruogagh6aeW0s",
                                 Time)),
                 0, 4),
    io:fwrite(Otp).
```

```
┌─[16:25][p-mihadyuk][~]
└╼ ~/Library/Application\ Support/ErlangInstaller/21.0/bin/erl -noshell -run a genotp -run init stop
rsF9
```
Токен получили, авторизовались - получили основной флаг.
 


