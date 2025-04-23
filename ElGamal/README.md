# Метод Эль-Гамаля
Алгоритм шифрования методом Эль-Гамаля основывается на алгоритме возведения в степень большого простого числа. Он является альтернативой алгоритму RSA.

Реализация алгоритма на Python:
```python
import random

class ElGamal:
  def __init__(self, p, q):
    self.p = p
    self.q = q

  def do(self, m):
    c = random.randint(2,self.p-2)
    d = self.mod_pow(self.q, c, self.p) 

    if m >= self.p:
      return "it's not coorect! m>=p!"

    k = random.randint(1,self.p-2) 
    R = self.mod_pow(self.q, k, self.p)
    E = m * self.mod_pow(d, k, self.p)

    m1 = (E * self.mod_pow(R, self.p - 1 - c, self.p)) % self.p
    self.check(m, m1)

  def check(self, m, m1):
    if m == m1:
      print ("match! - ", m, ", ", m1)
    else:
      print("messages don't match: ", m, ", ", m1)

  def mod_pow(self, base, exponent, modulus):
    result = 1
    base %= modulus
    while exponent > 0:
        if exponent % 2 == 1:
           result = (result * base) % modulus 
        base = (base * base) % modulus
    return result 
```
Выполним передачу какого-либо числа **m**. Предполагаем, что пересыл сообщениями происходит между собеседниками A и B (A - отправитель, B - получатель). Для начала для обоих абонентов выбираются некоторое большое простое число **p** и целое число **q**, причём q < p. На промежуточном этапе производится проверка **m** >= **p**, если это не так, **p** следует выбрать заново

Затем абонент A формирует воё секретное целое число **с**, причём 1 < c < p-1. В коде это число выбирается случайно с помощью функции randint. На основе этого числа A с помощью алгоритма быстрого возведения числа в степень вычисляет **d**

Далее абонент A генерирует число **k**, такое, что 0 < k < p - 1 и вычисляет **R** и **E**, где R=(q^k) mod p;
E=m*(d^k) mod p

Пару чисел (R,E) отправитель A отправляет своему собеседнику B, по которым последний вычисляет исходное сообщение: m1 = (E*R^(p-1-c)) mod p, где **c** - секретное число абонента B, вычисленное так же, как и **c** абонента А.

Если сообщения совпадают (т.е. алгоритм выполнен верно), то в результате получаем ответ: "match! - [*m*], [*m1*]".

Объяснение функции *mod_pow(base, exponent, modulus)* находится [тут](https://github.com/marchinn/algorithms/tree/22849c903e25f7cc14f5a54bc5903a48b02c2130/Fast%20exponentiation)

### Пример работы
Подставляя в алгоритм заранее выбранные значения p и q, зашифруем и расшифруем собщение message, а затем сравним исходное число и полученное:
```python
p = 139
q = 70
message = 53

E = ElGamal(p, q)
E.do(message)
```
Получаем результат:
> match! -  53,  53