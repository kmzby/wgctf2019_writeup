1. Четыре дня долбились в парараметры и пытались восстановить алгоритм 
работы магазина. Нашли html-inclusion и потенциальную XSS. Почти сразу
обнаружили доп флаг в robots.txt
2. В конце концов вернулись к изначальной идее брут-форса хэша, но, понимая 
бессмысленность этой затеи, атаковали гугл вопросами альтернативных путей 
обхода подписей (полагая, что используется md5). В конце концов запрос 
"md5 signature attack -collision" привел нас на гитхаб:
https://gist.github.com/yarjor/a8fd8633f2bdb06d0007263f34bb2af9 , и
оттуда на https://blog.skullsecurity.org/2012/everything-you-need-to-know-about-hash-length-extension-attacks
где мы и обнаружили замечательный инструмент hash_extender.
3. На главной магазина написано прямым текстом про "20 chars key", что 
существенно облегчило задачу.
```bash
ctf@ctf:~/hash_extender$ ./hash_extender -d 'type=simple&id=ls' -s 9a85c69d375cd2560dc35fa3a71aee87 -f md5 -a "type=leet&id=flag" -l 20 --out-data-format=html
Type: md5
Secret length: 20
New signature: 25322e9b748e2f1b85272a861c370cf0
New string: type%3dsimple%26id%3dls%80%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%28%01%00%00%00%00%00%00type%3dleet%26id%3dflag
```
и НАКОНЕЦ-ТО искомое
```bash
ctf@ctf:~/hash_extender$ curl "xxxshop.ctf2019.rocks/getexploit.php?type=simple&id=ls%80%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%28%01%00%00%00%00%00%00type=leet&id=flag&sign=25322e9b748e2f1b85272a861c370cf0" -o -
All params:type=simple&id=ls%80%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%00%28%01%00%00%00%00%00%00type=leet&id=flag&sign=25322e9b748e2f1b85272a861c370cf0<br>Sign params:type=simple&id=ls�(type=leet&id=flag<br>Type:simple<br>Id:flag<br><br>Calculated hash:<hidden><br>Sended hash:25322e9b748e2f1b85272a861c370cf0<br>You are 1337! Here is a flag for you: WGCTF{7B5A6DC61B9EA25448BACC7CE75A6056}
```
4. После этого страшный javascript уже не казался страшным, время поджимало, 
и самым очевидным вариантом поиска третьего флага стал брутфорс :)
Взяли самую первую строку
```javascript
var _0xeb8c=['SE9BRHI=','T2RtRmc=','TUdXc1o=','WUxTcXI=','bG9n','d2Fybg==','ZGVidWc=','aW5mbw==','ZXJyb3I=','dHJhY2U=','ZXhjZXB0aW9u','dGhpc2lzdmVyeWxvbmdzdHJpbmdmb3JvYmZ1c2NhdGlvbg==','bWQ1KA==','YXBwbHk=','cmV0dXJuIChmdW5jdGlvbigpIA==','e30uY29uc3RydWN0b3IoInJldHVybiB0aGlzIikoICk=','Y29uc29sZQ==']
```
каждую строковую переменную прогнали через base64 -d и md5sum
```bash
$ for i in `cat js`; do echo $i|base64 -d |md5sum; done
2ffedd44d5b2b8b93b7c217ea8ebbfa1  -
e0990ebb679ee8b0ba1926505ecf37d5  -
71e1bab431c79372d982537ab9c54202  -
81cffd13e0e123ec1af0dc7357aaf4d2  -
dc1d71bbb5c4d2a5e936db79ef10c19f  -
1ea4c3ab05ee0c6d4de30740443769cb  -
ad42f6697b035b7580e4fef93be20b4d  -
caf9b6b99962bf5c2264824231d7a40c  -
cb5e100e5a9a3e7f6d1fd97512215282  -
04a75036e9d520bb983c5ed03b8d0182  -
42552b1f133f9f8eb406d4f306ea9fd1  -
57def37f543cc216abbee246200129ac  -
76b807e6181d0951ab304be6804907c3  -
4da463dcb391d40ec0352edd2e42b2d2  -
2c5204d5167d8754bd95fa5cfc5ecae0  -
e3cdaeaa092f1880b5536606976b732f  -
bfafd813d7ea65ee4db1f09d7c8ffbf4  -
```
На 57 начинается только одна из них ('thisisverylongstringforobfuscation'),
она и оказалась флагом.