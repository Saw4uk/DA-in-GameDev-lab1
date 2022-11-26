# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #1 выполнил(а):
- Савченков Иван Алексеевич
- РИ210943
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | * | 60 |
| Задание 2 | * | 40 |
| Задание 3 | * | 0  |

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

## Дисклеймер
Я опять отошел от компьютера в процессе работы и макет отчета не сохранился. В скриншоты, вставленные в докзательство проделанной работу утеряны, поэтом, с вашего позволения, расскажу о подготовке к выполнению работы в текстовом формате.

## Подготовка

Сначала я установил пакеты в Unity

![image](https://user-images.githubusercontent.com/87923228/204092473-2e22fe1f-03ee-4210-bd85-4f15c895dfbc.png)

Затем я провел все необходимые манипуляции с анакондой. Виртуальное окружение MLAgent уже было установлено в папке проекта, поэтому я переустановил его. Затме оказалось, что необходимо снова установить torch - без него проект не работал. Я прописал pip install torch и все заработало. Затем я начал обучение через юнити.

Затем я скачал tensorflow

![image](https://user-images.githubusercontent.com/87923228/204092646-eac21f44-5b03-4fa4-8de5-3c1f12ac4d63.png)

Запустил tensorboard

![image](https://user-images.githubusercontent.com/87923228/204092684-0f281326-cbbc-4fdc-b1eb-ac51f7e08f18.png)

Посмотрел на свои результаты

![image](https://user-images.githubusercontent.com/87923228/204092877-af9b1630-086e-4487-afb6-3b31231bb0b8.png)


## Задание 1
### Определить, какие параметры, как влияют на обучение модели

Покопавшись в yaml файле я понял, что методом тыка будет разобраться очень трудно. Во первых потому, что увеличение каких-либо характеристик модели может быть неоднозначно мной интерпретировано. Во вторых, изменение разных характеристик может дать схожие изменения на графике обучения модели. Это связано с тем, что график мониторит лишь одно значение, а изменение параметров производит комплексный и глубинный эффект, поэтому я решил от метода практического познания перейти к теоретической обработке информации. Я нашел документацию на составления yaml файла и начал искать, какие значения, за что отвечают

```py
behaviors:
  Economic:
    trainer_type: ppo #Тип используемого тренажера: ppo, sac, или poca.
    hyperparameters:
      batch_size: 1024 #Количество опытов на каждой итерации градиентного спуска
      buffer_size: 10240 #Kоличество опыта, которое необходимо собрать перед обновлением модели
      learning_rate: 3.0e-4 #Начальная скорость обучения для градиентного спуска. Соответствует силе каждого шага обновления градиентного спуска.
      learning_rate_schedule: linear #Определяет, как скорость обучения изменяется с течением времени. 
      beta: 1.0e-2 # Сила регуляризации энтропии, которая делает стратегию "более случайной".
      epsilon: 0.2 # Влияет на то, насколько быстро стратегия может развиваться во время обучения.
      lambd: 0.95 # Параметр регуляризации (лямбда), используемый при вычислении обобщенной оценки преимуществ (GAE). Это можно рассматривать как то, насколько агент полагается на свою текущую оценку стоимости при вычислении обновленной оценки стоимости.
      num_epoch: 3 # Количество проходов через буфер опыта при выполнении оптимизации градиентного спуска 
    network_settings:
      normalize: false # Применяется ли нормализация к входным данным векторного наблюдения.
      hidden_units: 128 # Количество единиц в скрытых слоях нейронной сети. Соответствует количеству единиц в каждом полностью связном слое нейронной сети. 
      num_layers: 2 #  Количество скрытых слоев в нейронной сети.
    reward_signals:
      extrinsic:
        gamma: 0.99
        strength: 1.0
    checkpoint_interval: 500000 # Количество опыта, собранного тренером между каждой контрольной точкой.
    max_steps: 750000 #  Общее количество шагов (т.Е. собранных наблюдений и предпринятых действий), которые должны быть выполнены в среде (или во всех средах, если используется несколько параллельно) до завершения процесса обучения.
    time_horizon: 64 #  Сколько шагов опыта нужно собрать для каждого агента, прежде чем добавлять его в буфер опыта.
    summary_freq: 5000 #  Количество опыта, которое необходимо собрать перед созданием и отображением статистики обучения.
    self_play:
      save_steps: 20000 # Количество шагов тренажера между моментальными снимками.
      team_change: 100000 # Количество шагов trainer_steps между переключением обучающей команды.
      swap_steps: 10000 # Количество дополнительных шагов (не шагов тренера) между заменой политики противников другим снимком.
      play_against_latest_model_ratio: 0.5 #  Вероятность того, что агент будет играть против последней стратегии противника.
      window: 10 # Размер скользящего окна прошлых снимков, из которых выбираются оппоненты агента.
```

Тщательно изучив документацию я записал, какие параметры за что отвечают. Используя переводчик столкнулся с проблемой. Он упорно переводил слово policy как политика. Оно абсолютно не подходит по контексту к данным выражениям. Я постарался по контексту понять, что это слово означает и пришел к выводу, что здесь policy - это не политика, а стратегия обучения MLAgent. Аргументирую это значением параметра "play_against_latest_model_ratio". В оригинале его смысл записан так: "Probability an agent will play against the latest opponent policy." Если переводить дословно, то получится: "Вероятность того, что агент будет играть против последней политики противника." Здесь это слово абсолютно не подходит, а слово стратегия вписывается идеально. Отсюда делаем вывод, что в контексте MLAgent - policy это стратегия

## Задание 2
### Описать результаты, выведенные в tensorboard

Вот мои результаты в tensorboard. 
![image](https://user-images.githubusercontent.com/87923228/204092877-af9b1630-086e-4487-afb6-3b31231bb0b8.png)

Что можно о них сказать. Это очень похоже на реальную экономику. Давайте посмотрим на левую верхнюю картинку. Это график вознаграждений, кго можно приравнять к графику прибыльности наших золотых приисков. Что же мы из него нублюдаем? Стандуртную для экономики картину. Видимо в данном случае случился кризис. Скорее всего, на это повлияли случайно меняющиеся величины


## Выводы

Исходя из проделанной работы можно сделать вывод. Во первых - эффективность самообучаемого алгоритма растет по мере увеличения количества итераций.  Во вторых самообучаемый алгоритм позволяет достичь высокой точности рассчетов при прогнозах. Это очень полезно для симуляции экономики, например.


| GitHub | [https://github.com/Saw4uk/DA-in-GameDev-lab1)] |
| Google Drive | [https://drive.google.com/drive/u/0/folders/1hHAR0jKNRbnM2ViSx3jJJBN7zfejcbe8] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
