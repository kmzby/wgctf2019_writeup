1. Декомпилируем jar и получаем ```return 32L;```
Сервер не соглашается с нашим решением. Горим, в итоге пробуем более другой 
декомпилятор (procyon), и получаем правильный код
```
  private static long calculateResponse(long challenge) {
        challenge %= 100L;
        challenge -= 128L;
        challenge += 32L;
        return challenge;
    }
```
При помощи https://www.compilejava.net/ проходим аутентификацию
```
import java.lang.Math;

public class HelloWorld
{
  public static void main(String[] args)
  {

    final String userResponse = "3913204043336420948";
    long challenge = Long.parseLong(userResponse);
    System.out.print(Long.toString(calculateResponse(challenge)));
  }

  private static long calculateResponse(long challenge) {
        challenge %= 100L;
        challenge -= 128L;
        challenge += 32L;
        return challenge;
    }
}
```
и забираем флаг.
```
LISTDB
db.public
db.private
USE db.public
Selected database db.public
GET flag
WGCTF{5E3CF5A9663A422ABCC92853323810D5}
```

2. Определенно, в db.private должен быть доп.флаг
Из CommandParser.java понимаем, что нас интересуют команды
 STARTPAUTH и PAUTH
В ConnectionContext находим
```
    public int getPrivateAuthChallenge() {
        this.privatePRGN = new PrivatePRGN();
        return this.privatePRGN.nextInt();
    }
```
и саму считалку
```
public PrivatePRGN() {
        this.currentState = ThreadLocalRandom.current().nextInt(1, 10);
    }
    
    public int nextInt() {
        final int num = (this.currentState + 251) % 255;
        return this.currentState = num;
    }
```
Аутентифицируемся
 ```python
$ ipython
Python 2.7.16 (default, Mar 12 2019, 11:32:42)
Type "copyright", "credits" or "license" for more information.

In [1]: (3 + 251) % 255
Out[1]: 254

```
 
 и с третьей попытки получаем желаемое
```
STARTPAUTH
3
PAUTH 254
Authentication successful
USE db.private
Selected database db.private
LISTKEYS
0e6965a1-2ff6-4129-bd7a-ac025eb230f3
294edbb2-c685-4811-9df2-e5ad0fede09a
374d228e-7e82-4234-ac0b-37ac3e0cf0a2
7ad59fd1-7ce3-4c31-9fc0-ec942033b739
5fad519d-7f39-4eb3-841a-9d34a47060a4
cbaacf05-db1a-46b3-9a07-4e854f1f3cc1
1084e9e1-b5a6-48d0-a226-3c81495891c3
1b0b68dc-4853-46bd-98f1-8bcd35e0ddc1
d402b84d-67b7-4cbf-9cd7-e49b8e63e26b
GET 0e6965a1-2ff6-4129-bd7a-ac025eb230f3
Some data
GET 294edbb2-c685-4811-9df2-e5ad0fede09a
Some data
GET 374d228e-7e82-4234-ac0b-37ac3e0cf0a2
WGCTF{109CC1652C85EE88F1FFB8C949FADFA3}
```

3. В PrivatePRGN.java замечаем какой-то дикий код
```
public String part2CalculationBuggyAndUnfinished() {
        final int n;
        return this.calculateMD5(IntStream.iterate(1, i -> {
            i++;
            return n;
        }).limit(20L).mapToObj(i -> String.valueOf(i)).collect((Collector<? super Object, ?, String>)Collectors.joining()));
    }
    
    public String calculateMD5(final String string) {
        final String hashSuffixAndPrefix = "WGCTF{%s}";
        throw new NotImplementedException();
    }
```
Очевидно, это недописанная функциональность, которая должна генерировать корректный флаг.
Коллекции очень долго (больше суток) мешали посмотреть на код "правильно", но в итоге
было замечено основное: итератор `i=1, i++`, от вывода которого берутся первые 20 чисел (не цифр!)
 ```bash
┌─[16:18][p-mihadyuk][~]
└╼ echo -n 1234567891011121314151617181920 | md5sum
52c46dff81346ead02fcf6245c762b1a  -
```