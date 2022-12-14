# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #2 выполнил(а):
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
### Реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity. 

Сначала я подключил API Google Sheets, который необходим для организации взаимодействия листов с кодом.

![image](https://user-images.githubusercontent.com/87923228/195067557-07f95151-3249-42a5-821e-75f1a5e62847.png)

Затем написал код на Python, который заполнаяет Google Sheet случайными значениями.

```py
import gspread
import numpy as np
gc = gspread.service_account(filename="unitydatascience-364811-e0902db64fd4.json")
sh = gc.open('UnitySheets')
price = np.random.randint(2000,10000,11)
mon = list(range(1,11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        tempInf = ((price[i-1]-price[i-2])/price[i-2])*100
        tempInf = str(tempInf)
        tempInf = tempInf.replace(".",",")
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(price[i-1]))
        sh.sheet1.update(('C' + str(i)), str(tempInf))
        print(tempInf)
```

Туда я вставил имя файла Google Sheets, к которому буду обращаться из Unity. После запуска программы значения, находящиеся в ячейках В1-В11 и С1-С11 заполняются случайными значениями из заданного диапазона, в ячейках А1-11 записаны порядковые номера элементов. Ниже представлены полученные значения.

![image](https://user-images.githubusercontent.com/87923228/195070703-df838673-c044-4ffb-b75e-f1929265739b.png)

Следующим шагом было написание кода на юнити. Я немного отклонился от шаблона и вместо трех методов вызова звука я написал один, в который можно передавть звуковую дорожку. Так я избежал дублирования кода.

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip GoodSpeak;
    public AudioClip NormalSpeak;
    public AudioClip BadSpeak;

    private AudioSource selectAudio;

    private Dictionary<string,float> dataSet = new Dictionary<string,float>();
    private bool statusStart = false;

    private int i = 1;
    // Start is called before the first frame update
    void Start()
    {
        StartCoroutine(GoogleSheets());
    }

    // Update is called once per frame
    void Update()
    {
        if (statusStart == false && i != dataSet.Count)
        {
            if (dataSet["Mon_" + i] <= 10)
            {
                StartCoroutine(PlayerPlayAudio(GoodSpeak,3));
                Debug.Log(dataSet["Mon_" + i]);
            }

            if (dataSet["Mon_" + i] > 10 && dataSet["Mon_" + i] < 100)
            {
                StartCoroutine(PlayerPlayAudio(NormalSpeak,3));
                Debug.Log(dataSet["Mon_" + i]);
            }

            if (dataSet["Mon_" + i] >= 100)
            {
                StartCoroutine(PlayerPlayAudio(BadSpeak,4));
                Debug.Log(dataSet["Mon_" + i]);
            }
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest currentResp =
            UnityWebRequest.Get(
                "https://sheets.googleapis.com/v4/spreadsheets/1yvNqt1oZZr_-0qQleYdb_MskFP6eVn8oXfzdhMATnZE/values/LIST1?key=AIzaSyDcNwizJ4AW5nHk-LibeEsBVRI6ZAckFVg");
        yield return currentResp.SendWebRequest();
        var rawResp = currentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
        Debug.Log(dataSet["Mon_1"]);
    }

    IEnumerator PlayerPlayAudio(AudioClip clip,int time)
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = clip;
        selectAudio.Play();
        yield return new WaitForSeconds(time);
        statusStart = false;
        i++;
    }
}
```


В данном коде функция PlayerPlayAudio отвечает за проигрывание аудиодорожки, IEnumerator GoogleSheets() возвращает значения из Google Sheet и делает это лениво, с помощью yield return, в методе Update проверяется возвращенное значение, и в зависимости от величины значения воспроизводится соответственный звук.

После запуска проекта в юнити, значения из листа поступили в программу и были выведены на консоль в юнити, с воспроизведением соответствующего звука.
![image](https://user-images.githubusercontent.com/87923228/195069644-a6a05bc7-1cb1-4ce0-92c9-09ceed0e81b3.png)

## Задание 2
### Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1. 

Для того, чтобы в юнити внести все необходимые данные воспользуемся ранее написанным, но немного модифицируемым алгоритмом.

```py

import gspread
import numpy as np
x = [3, 21, 22, 34, 54, 34, 55, 67, 89, 99]
x = np.array(x)
gc = gspread.service_account(filename="unitydatascience-364811-e0902db64fd4.json")
sh = gc.open('UnitySheets')
price = np.random.randint(2000,10000,11)
mon = list(range(1,11))
i = 0
while i < len(x):
    i += 1
    if i == 0:
        continue
    else:
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(price[i-1]))
        sh.sheet1.update(('C' + str(i)), str(x[i-1]))

```

После запуска данной программы с Google Sheet происходило следующее

https://user-images.githubusercontent.com/87923228/195074426-227bfa4d-daf4-4b3a-a81d-fc92a54b1a15.mp4


Эти-же значения выводятся и в Unity

![image](https://user-images.githubusercontent.com/87923228/195074720-173fb3b0-d794-4db4-9922-f98bf4b1b6fd.png)

## Задание 3
### Самостоятельно разработать сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2.

Для закрепления изученного материала я захотел в 3-м задание реализовать аглоритм, который отражает нестабильность народа. С вероятностью в 50% народ будет или хвалить вас или ругать. Я решил реализовать это с помощью остатка от деления на 2. Если число делится на 2, то это хорошо, а если нет, то плохо. Использовать буду данные, полученные с помощью линейной регрессии. Для работы программы переписал метод update в C# скрипте.

```c#
    void Update()
    {
        if (statusStart == false && i != dataSet.Count)
        {
            Debug.Log(dataSet["Mon_" + i]%2);
            if (dataSet["Mon_" + i] % 2 == 0)
            {
                StartCoroutine(PlayerPlayAudio(GoodSpeak,3));
            }
            else
            {
                StartCoroutine(PlayerPlayAudio(BadSpeak,4));
            }
        }
    }
```

После запуска проекта воспроизводятся звуки и выводятся 0 и 1.

![image](https://user-images.githubusercontent.com/87923228/195076291-af000fee-c72c-4a92-abe1-7393a2a8c41b.png)


## Выводы

Исходя из проделанной работы можно сделать вывод. Во первых - связка Unity-Python-Google Sheets позволит в будущем реализовывать куда более сложные механизмы. Сочетание практически безграничного функционала библиотек Python с возможностью хранения данных на Google Sheets открывает возможности для широкого анализа полученных из игры данных с помощью Python и использования проанализированных данных в игре. Это позволит использовать серьезные алгоритмические структуры в наших играх. За эту лабораторную я ознакомился с воспроизведением звука в Unity, создал API ключ для Google Sheets и научился связывать Google Sheets, Python и Unity/


| GitHub | [https://github.com/Saw4uk/DA-in-GameDev-lab1)] |
| Google Drive | [https://drive.google.com/drive/u/0/folders/1hHAR0jKNRbnM2ViSx3jJJBN7zfejcbe8] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
