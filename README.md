# Лабораторная работа № 4
### Задание 1.
На основе приложенного кода в файле gen_fib.py разработали сопрограмму (корутину), реализующую возвращение списка элементов ряда Фибоначчи.
 файл gen_fib.py
 ```python
import functools

def fib_elem_gen():
    a = 0
    b = 1

    while True:
        yield a
        res = a + b
        a = b
        b = res

g = fib_elem_gen()

while True:
    el = next(g)
    print(el)
    if el > 10:
        break

def my_genn():
    """Сопрограмма"""
    while True:
        number_of_fib_elem = yield
        a, b = 0, 1
        l = [str(number_of_fib_elem) + ":"]  # Чтобы сохранять результаты
        for _ in range(number_of_fib_elem):
            l.append(a)
            a, b = b, a + b
        yield l

def fib_coroutine(g):
    @functools.wraps(g)
    def inner(*args, **kwargs):
        gen = g(*args, **kwargs)
        gen.send(None)  
        return gen
    return inner

# Применяем декоратор
my_genn = fib_coroutine(my_genn)
gen = my_genn()

# Отправляем число, и получаем результат
print(gen.send(6))  # Вернет первые 5 чисел Фибоначчи
```
файл test_fib.py
```python
import pytest

# Функция, которая возвращает первые n элементов последовательности Фибоначчи
def fib(n):
    """Возвращает список первых n элементов последовательности Фибоначчи"""
    if n <= 0:
        return []
    elif n == 1:
        return [0]
    elif n == 2:
        return [0, 1]
    
    a, b = 0, 1
    result = [0, 1]
    for _ in range(2, n):
        a, b = b, a + b
        result.append(b)
    return result


# Тесты
def test_fib_1():
    """Тривиальный случай n = 1"""
    assert fib(1) == [0], "fib(1) должно быть [0]"

def test_fib_2():
    """Проверка fib(2)"""
    assert fib(2) == [0, 1], "fib(2) должно быть [0, 1]"

def test_fib_3():
    """Тест для случая n = 3"""
    assert fib(3) == [0, 1, 1], "fib(3) должно быть [0, 1, 1]"

def test_fib_5():
    """Тест для случая n = 5"""
    assert fib(5) == [0, 1, 1, 2, 3], "fib(5) должно быть [0, 1, 1, 2, 3]"

# Запуск тестов с помощью pytest
if __name__ == "__main__":
    pytest.main()
```

### Задание 2.
Дополним код классом FibonacchiLst , который позволяет перебирать элементы из ряда Фибоначчи по данному ей списку. Итератор должен вернуть очередное значение, которое принадлежит ряду Фибоначчи, из данного ей списка

  файл fibonacchi_lst.py

  ```python
class FibonacchiLst:
    def __init__(self, lst):
        """Инициализация списка и генерация чисел Фибоначчи до максимального элемента списка"""
        self.lst = lst
        self.fib_set = self._generate_fib_set(max(lst))

    def _generate_fib_set(self, max_value):
        # Генерация множества чисел Фибоначчи до максимального значения
        a, b = 0, 1
        fib_set = {a, b}
        while b <= max_value:
            a, b = b, a + b
            fib_set.add(b)
        return fib_set

    def __iter__(self):
        # Возвращаем итератор для перебора списка
        self.idx = 0
        return self

    def __next__(self):
        #Возвращаем следующий элемент из списка, если он принадлежит ряду Фибоначчи
        while self.idx < len(self.lst):
            value = self.lst[self.idx]
            self.idx += 1
            if value in self.fib_set:
                return value
        raise StopIteration

if __name__ == "__main__":
    lst = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 1]
    fib_iterator = FibonacchiLst(lst)

    print(list(fib_iterator))
```
