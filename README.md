# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #1 выполнил(а):
- Савченков Иван Алексеевич
- РИ210943
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 20 |
| Задание 3 | * | 20 |

знак "*" - задание выполнено; знак "#" - задание не выполнено;

Работу проверили:
- к.т.н., доцент Денисов Д.В.
- к.э.н., доцент Панов М.А.
- ст. преп., Фадеев В.О.

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Ознакомиться с основными операторами зыка Python на примере реализации линейной регрессии.

## Задание 1
### Написать программы Hello World на Python и Unity

Такой код я использовал на питоне. Так как пайтон - это интерпретируемый язык я могу позволить себе не использовать лишние синтаксические конструкции, одной строчки достаточно. Код запускался на Google Collab. Интерпритатор Python может выполнять программы, будучи загруженным на сервер, поэтому я могу использовать облачные сервисы для быстрой проверки программ. Такой-же особенностью обладет, например, Javascript.

```py
print('Hello world')
```

Это код из Unity. Язык c# не позволяет так-же просто как и питон вывести на экран "Hello world", написав всего одну строчку, так как это компилируемый язык. C# прекрасно показываает себя в Unity как Обьектно-Ориентированный язык.

```c#
using UnityEngine;

public class odsfgj : MonoBehaviour
{
    // Start is called before the first frame update
    void Start()
    {
        Debug.Log("Hello world");
    }
}
```

## Задание 2
### В разделе "Ход работы" пошагово выполнить каждый пункт с описанием и примером реализации задачи по теме лабораторной работы

Работу буду выполнять в IDE PyCharm потому, что в рамках обязательного курса использую её. В облачных сервисах код исполняется с задержкой и требует времени на ожидание проверки. В случаях, когда вычислительных мощностей вашего ПК недостаточно это имеет смысл, но в моей ситуации считаю целесообразным использовать IDE на стационарном компьютере, это сократит время отклика и ускорит процесс.

Сначала я провел подготовку данных для работы с алгоритмом линейной регрессии

```py

import numpy as np
import matplotlib.pyplot as plt

x = [3, 21, 22, 34, 54, 55, 67, 89, 99]
x = np.array(x)
y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
y = np.array(y)

plt.scatter(x, y)

```

Затем я перенес математические функции.


```py
import numpy as np
import matplotlib.pyplot as plt

x = [3, 21, 22, 34, 54, 55, 67, 89, 99]
x = np.array(x)
y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
y = np.array(y)

plt.scatter(x, y)


def model(a, b, x):
    return a * x + b


def loss_function(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    return (0.5 / num) * (np.sqare(prediction - y)).sum()


def optimize(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    deltaA = (1.0 / num) * ((prediction - y) * x).sum()
    deltaB = (1.0 / num) * ((prediction - y).sum())
    a = a - Lr * deltaA
    b = b - Lr * deltaB
    return a, b


def iterate(a, b, x, y, times):
    for i in range(times):
        a, b = optimize(a, b, x, y)
    return a,b
```

Связаны фунции iterate и optimize. Резонный вопрос, каим образом они связаны. В функции optimize выполняется алгоритм градиентного спуска. Насолько я понял, с его помощью мы можем обучить модель строить линейную регрессию. Для этого нужно, чтобы алгоритм повторялся много раз. Количество посторений передается в функцию iterate.

Также связаны функции model и loss_function. Функция модели нужна для предсказания результата в функции рассчета среднеквадратичной ошибки.

После переноса функций я начал итерацию. Для этого мне пришлось разобраться с параметром Lr, о котором я написал в задании 3. Запускал я итерации в Google Collab, предварительно попарвив ошибки в коде. Итоговая версия для теста выглядит так.

```py
import numpy as np
import matplotlib.pyplot as plt

x = [3, 21, 22, 34, 54, 34, 55, 67, 89, 99]
x = np.array(x)
y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
y = np.array(y)

plt.scatter(x, y)


def model(a, b, x):
    return a * x + b


def loss_function(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    return (0.5 / num) * (np.square(prediction - y)).sum()


def optimize(a, b, x, y, Lr):
    num = len(x)
    prediction = model(a, b, x)
    deltaA = (1.0 / num) * ((prediction - y) * x).sum()
    deltaB = (1.0 / num) * ((prediction - y).sum())
    a = a - Lr * deltaA
    b = b - Lr * deltaB
    return a, b


def iterate(a, b, x, y, times, learn_rate):
    for i in range(times):
        a, b = optimize(a, b, x, y,learn_rate)
    return a, b


a = np.random.rand(1)
print(a)
b = np.random.rand(1)
print(b)
Lr = 0.000001

a, b = iterate(a, b, x, y, 10000,Lr)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)
```

Вот результаты первой итерации

[0.69216716]
[0.04986405]
[0.69549611] [0.04991171] 1942.5837279794007
[<matplotlib.lines.Line2D at 0x7f121b5c4e50>]

![image](https://user-images.githubusercontent.com/87923228/191196408-7deabf88-7630-4439-9c37-4a25d2081e06.png)

Второй итерации

[0.93455782]
[0.30316897]
[0.93966136] [0.30324049] 1216.868876417384
[<matplotlib.lines.Line2D at 0x7f121be37dd0>]

![image](https://user-images.githubusercontent.com/87923228/191196360-a456a315-c80d-4f8e-9a44-79efeada1e72.png)

Третьей итерации

[0.12109289]
[0.8610943]
[0.13629475] [0.86131602] 4229.302780317601
[<matplotlib.lines.Line2D at 0x7f121b5a42d0>]

![image](https://user-images.githubusercontent.com/87923228/191196286-1dc3b727-8cf5-4f91-831d-47491067d0ff.png)

Четвертой итерации

[0.98758956]
[0.41456584]
[0.99708053] [0.41469788] 1072.5604219576526
[<matplotlib.lines.Line2D at 0x7f121b626290>]

![image](https://user-images.githubusercontent.com/87923228/191196210-a2677922-1c87-441f-9004-e0d7c1b39442.png)

Пятой итерации

[0.56623783]
[0.36694814]
[0.58466764] [0.3672132] 2312.946368460194
[<matplotlib.lines.Line2D at 0x7f121b4df6d0>]

![image](https://user-images.githubusercontent.com/87923228/191196474-e6ae2652-0388-44ed-99c0-7752c4b9e302.png)

Десятитысячной итерации

[0.05341429]
[0.43897414]
[1.74655408] [0.43351483] 190.07975691902615
[<matplotlib.lines.Line2D at 0x7f121b454f10>]

![image](https://user-images.githubusercontent.com/87923228/191196567-d1f33b6d-b22e-47af-b442-87dd9155bdaa.png)

Еще примеры десятитысячных итераций

![image](https://user-images.githubusercontent.com/87923228/191196101-cd25c099-3753-4b3f-a941-2ebe14b7bf63.png)

![image](https://user-images.githubusercontent.com/87923228/191196132-2c18d37c-a617-4dbb-9502-a6cb41355e92.png)

![image](https://user-images.githubusercontent.com/87923228/191196631-3f639482-f078-4a15-b236-6fb5c412e585.png)

Как видно из графиков по мере увеличения количества итераций эффективность построения предположения растет. 



## Задание 3
### Должна ли величина loss стремиться к 0 при изменении исходных данных?

Однозначно да. Иначе зачем этот алгоритм? loss - величина показывающая количество потерь. По мере увеличения нами количества итераций мы наблюдаем уменьшение этой величины. 

Первая итерация
[0.17433163]
[0.02935553]
[0.66991467] [0.03659929] 2029.1517583238867
[<matplotlib.lines.Line2D at 0x7f121b48c890>]
![image](https://user-images.githubusercontent.com/87923228/191198367-4667e932-9b61-46a3-b341-44d2c315d308.png)

Десятая

[0.16113125]
[0.31945804]
[1.71137446] [0.33996617] 191.89427054029227
[<matplotlib.lines.Line2D at 0x7f121ae740d0>]
![image](https://user-images.githubusercontent.com/87923228/191198806-5337e19e-b759-4c2c-accc-ac73e6ecda67.png)

Сотая

[0.79775035]
[0.62858475]
[1.74384777] [0.61126646] 190.63855889580995
[<matplotlib.lines.Line2D at 0x7f121adf5910>]
![image](https://user-images.githubusercontent.com/87923228/191198853-2bacc197-edfe-4ba2-a722-5964189ffe9c.png)

Тысячная

[0.02528197]
[0.99612263]
[1.74251649] [0.69870587] 190.9166012643584
[<matplotlib.lines.Line2D at 0x7f121ad76190>]
![image](https://user-images.githubusercontent.com/87923228/191198925-0a4f1e18-3f12-49d2-bf26-3ed07a0f529e.png)


Десятитысячная 

[0.18231691]
[0.98177152]
[1.78149165] [-1.86120177] 183.63825794898045
[<matplotlib.lines.Line2D at 0x7f121aced990>]
![image](https://user-images.githubusercontent.com/87923228/191198978-da47416f-5178-4aa4-8c49-cb4d2de19752.png)

### Какова роль параметра Lr? Ответьте на вопрос, приведите пример выполнения кода, который подтверждает ваш ответ. В качестве эксперимента можете изменить значение параметра.
Методом научного гугла я выяснил, что алгоритм градиентного спуска требует параметра скорости обучения. Методом исключения я выяснил, что переменная Lr как раз подходит под это значение. Я решил изменить код, чтобы эту переменную можно было передать через функцию optimize.

```py
import numpy as np
import matplotlib.pyplot as plt

x = [3, 21, 22, 34, 54, 34, 55, 67, 89, 99]
x = np.array(x)
y = [2, 22, 24, 65, 79, 82, 55, 130, 150, 199]
y = np.array(y)

plt.scatter(x, y)


def model(a, b, x):
    return a * x + b


def loss_function(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    return (0.5 / num) * (np.square(prediction - y)).sum()


def optimize(a, b, x, y, Lr):
    num = len(x)
    prediction = model(a, b, x)
    deltaA = (1.0 / num) * ((prediction - y) * x).sum()
    deltaB = (1.0 / num) * ((prediction - y).sum())
    a = a - Lr * deltaA
    b = b - Lr * deltaB
    return a, b


def iterate(a, b, x, y, times, learn_rate):
    for i in range(times):
        a, b = optimize(a, b, x, y,learn_rate)
    return a, b


a = np.random.rand(1)
print(a)
b = np.random.rand(1)
print(b)
Lr = 0.000001

a, b = iterate(a, b, x, y, 10000,Lr)
prediction = model(a, b, x)
loss = loss_function(a, b, x, y)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)
```

Теперь мы можем задать скорость прямо через функцию optimize.

## Выводы

Исходя из проделанной работы можно сделать вывод. Во первых - эффективность самообучаемого алгоритма растет по мере увеличения количества итераций.  Во вторых самообучаемый алгоритм позволяет достичь высокой точности рассчетов при прогнозах. Это очень полезно для симуляции экономики, например.


| GitHub | [https://github.com/Saw4uk/DA-in-GameDev-lab1)] |
| Google Drive | [https://drive.google.com/drive/u/0/folders/1hHAR0jKNRbnM2ViSx3jJJBN7zfejcbe8] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
