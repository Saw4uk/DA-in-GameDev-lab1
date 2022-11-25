# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #4 выполнил(а):
- Савченков Иван Алексеевич
- РИ210943
Отметка о выполнении заданий (заполняется студентом):

| Задание | Выполнение | Баллы |
| ------ | ------ | ------ |
| Задание 1 | # | 60 |
| Задание 2 | # | 20 |
| Задание 3 | # | 20 |

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
Я пишу этот отчет во второй раз, потому, что первый отчет у меня стерся, после того, как я, по своей тупости, не сохранил его перед коммитом. Коммит, дал ошибку и я потерял всё.

![image](https://user-images.githubusercontent.com/87923228/204015413-0561ab8c-c795-4496-8830-cad5c5e8c6b3.png)

Прошу простить меня за мою, возможно, излишнюю краткость в этом отчете, но я искринне доволен проделанной работой.

## Цель работы
Ознакомиться с принципами работы перцептрона.

## Задание 1
### В проекте Unity реализовать перцептрон

Перенес предоставленный код

```c#
using System.Collections;
using System.Collections.Generic;
using UnityEngine;

[System.Serializable]
public class TrainingSet
{
	public double[] input;
	public double output;
}

public class Perceptron : MonoBehaviour {

	public TrainingSet[] ts;
	double[] weights = {0,0};
	double bias = 0;
	double totalError = 0;

	double DotProductBias(double[] v1, double[] v2) 
	{
		if (v1 == null || v2 == null)
			return -1;
	 
		if (v1.Length != v2.Length)
			return -1;
	 
		double d = 0;
		for (int x = 0; x < v1.Length; x++)
		{
			d += v1[x] * v2[x];
		}

		d += bias;
	 
		return d;
	}

	double CalcOutput(int i)
	{
		double dp = DotProductBias(weights,ts[i].input);
		if(dp > 0) return(1);
		return (0);
	}

	void InitialiseWeights()
	{
		for(int i = 0; i < weights.Length; i++)
		{
			weights[i] = Random.Range(-1.0f,1.0f);
		}
		bias = Random.Range(-1.0f,1.0f);
	}

	void UpdateWeights(int j)
	{
		double error = ts[j].output - CalcOutput(j);
		totalError += Mathf.Abs((float)error);
		for(int i = 0; i < weights.Length; i++)
		{			
			weights[i] = weights[i] + error*ts[j].input[i]; 
		}
		bias += error;
	}

	double CalcOutput(double i1, double i2)
	{
		double[] inp = new double[] {i1, i2};
		double dp = DotProductBias(weights,inp);
		if(dp > 0) return(1);
		return (0);
	}

	void Train(int epochs)
	{
		InitialiseWeights();
		
		for(int e = 0; e < epochs; e++)
		{
			totalError = 0;
			for(int t = 0; t < ts.Length; t++)
			{
				UpdateWeights(t);
				Debug.Log("W1: " + (weights[0]) + " W2: " + (weights[1]) + " B: " + bias);
			}
			Debug.Log("TOTAL ERROR: " + totalError);
		}
	}

	void Start () {
		Train(8);
		Debug.Log("Test 0 0: " + CalcOutput(0,0));
		Debug.Log("Test 0 1: " + CalcOutput(0,1));
		Debug.Log("Test 1 0: " + CalcOutput(1,0));
		Debug.Log("Test 1 1: " + CalcOutput(1,1));		
	}
	
	void Update () {
		
	}
}

```

Реализовал логическое ИЛИ

![image](https://user-images.githubusercontent.com/87923228/204015732-ac5782bd-481f-4285-983c-3e1e647d9ef9.png)

Результат получил уже на третьей итерации

Реализовал логическое И

![image](https://user-images.githubusercontent.com/87923228/204015845-eb20c910-2433-4d2b-a617-93613eeadf36.png)

Результат получен быль только на 8-й итерации

NAND

![image](https://user-images.githubusercontent.com/87923228/204016092-f20a52dc-4665-48c7-9842-ebf22bda3d17.png)

Результат был на 4-й итерации

XOR 

![image](https://user-images.githubusercontent.com/87923228/204016177-d8790904-ca91-4c6c-9e87-5dc14a13101c.png)

XOR реализовать не получилось, решил найти, что еще нельзя реализовать с поможью перцептрона.
Оказалось, что NOR, как и XOR, неподвластен перцептрону.

![image](https://user-images.githubusercontent.com/87923228/204016390-d415e838-4ca7-47e4-bd81-a65aeba5cdbd.png)

Я нашел картинку, прекрасно иллюстрирующую, почему эти операции не могут быть реализованы в данной модели.

![image](https://user-images.githubusercontent.com/87923228/204016682-c03aafff-3b0b-43b8-8465-41c209647f1d.png)

## Задание 2
### Построить графики зависимости количества эпох от ошибки.

![image](https://user-images.githubusercontent.com/87923228/204017064-53167198-6816-4941-842e-312b7c34009f.png)

Исходя из представленных выше графиков, можно сделать вывод, что наблюдается обратная зависимость количества ошибок, от количства эпох. Другими словами, чем больше эпох, тем меньше ошибок. Безусловно, есть и исключения и я видел в процессе работы, как количество ошибок в следующей эпохе было больше, чем в предыдущей, но это исключение не меняет основную тендецию. И с каждой новой эпохой вероятность того, что перцептрон отработает без ошибок возрастает. 

От чего зависит количество эпох? От сложности ожидаемой операции. Чем сложнее операция, тем больше нужно учиться перцептрону. Чем больше входных данных тем больше эпох нужно. Но мы не сможем предсказать заранее, какое количество эпох понадобится перцептрону для обучения. Приведу пример. 

Вот обучение перцептрона логическому или. За три запуска итерации в которых ошибка впервые была равна нулю отличаются.

В этом случае на второй попытке перцептрон обучился.
![image](https://user-images.githubusercontent.com/87923228/204017967-dd38ec93-369c-4a84-b4f4-399505259cae.png)

А в этом случае вообще сразу же.
![image](https://user-images.githubusercontent.com/87923228/204018137-6c17909a-95ca-4fe6-8122-7d0c06aa9a38.png)

Здесь же, на четвертой попытке.
![image](https://user-images.githubusercontent.com/87923228/204018273-da1fdb63-d465-4a63-ab63-6e5274f7a078.png)

Это доказывает, что зависимость есть, но заранее определить необходимое количество эпох невозможно.


## Задание 3
### Визулазировать работу перцептрона в Unity.

Решил сделать визуализацию работы всех возможных вариантов работы перцептрона при входных данных размером в 2, тоесть 4 варианта. Сначала сделал тестовую сцену.

![image](https://user-images.githubusercontent.com/87923228/204019680-6ebb29fd-58ff-4ab2-a5a6-bf019508c3a7.png)

На ней кубики будут соприкасаться, и при их моприкосновении их данные будут прогоняться через перцептрон и будет выводиться рещультат в виде окрашивания падающего кубика.

Сделал простейший класс данных кубика, к которому будет обращаться PerceptronController, чтобы считать данные из кубика.

```c#
public class CubeData : MonoBehaviour
{
    [SerializeField] private int data;

    public int Data => data;
}
```
Затем я прикрепил его на все кубы и задал значения. Черный - 0, Белый - 1.

После я создал небольшой класс PerceptroneController, который будет отвечать за хранение информации о перцептроне. В будущем это позволит переключаться между перцептронами, чтобы наблюдать разный результат, а не закреплять перцептрон руками. 

```c#
public class PerceptronController : MonoBehaviour
{
    [SerializeField]
    public Perceptron Perceptron { get; set; }

    private Color zeroColor;
    private Color oneColor;

    public Color ZeroColor => zeroColor;

    public Color OneColor => oneColor;

    void Awake()
    {
        zeroColor = Color.black;
        oneColor = Color.white;
    }
    
}
```

Написал класс логики для падающих кубов.

```c#
public class TriggerCubeLogic : MonoBehaviour
{
    private PerceptronController perceptronController;

    public void Awake()
    {
        perceptronController = FindObjectOfType(typeof(PerceptronController)) as PerceptronController;
    }

    public void OnTriggerEnter(Collider other)
    {
        var perceptrone = perceptronController.perceptron;
        GetComponent<Renderer>().material.color =
            perceptrone.CalcOutput(GetComponent<CubeData>().Data, other.GetComponent<CubeData>().Data) == 1
                ? perceptronController.OneColor
                : perceptronController.ZeroColor;
    }
}
```

Кубик будет падать и вызывать событие OnTriggerEnter. Затем он будет обращаться к контроллеру перцептронов и в зависимости от результата выполнения на входных данных из кубиков выдавать результат.

Так как Контроллер фактически Singleton, то я считаю оправданным в данной ситуации обращение по имени обьекта, так как в текущей обстановке гарантируется, что обьект будет один. Нужно отметить, что при моей концепции это не ухудшает расширяемость кода и не является плохой практикой. Это позволит мне создавать сколько угодно кубов, при этом не затрачивая никаких усилий на указывание этим кубам на контроллер, с моей стороны нужно просто гарантирвоать его единственность.

Затем сделал небольшой интерфейс для перключения перцептрона. По нажатию кнопки можно будет изменить текущий перцептрон и, соответственно, логику работы программы.

![image](https://user-images.githubusercontent.com/87923228/204025845-b01e2ff9-7638-4b0c-9e38-49351e156ee2.png)

Написал класс логики кнопки

```c#
public class PerceptronButtonLogic : MonoBehaviour
{
    [SerializeField] private Perceptron perceptron;
    
    private PerceptronController perceptronController;

    public void Awake()
    {
        perceptronController = FindObjectOfType(typeof(PerceptronController)) as PerceptronController;
        GetComponent<Button>().onClick.AddListener(OnClick);
    }

    private void OnClick()
    {
        perceptronController.perceptron = perceptron;
        perceptronController.UpAllCubes();
    }
}
```

По нажатию на неё у контроллера буте меняться перцептрон и подниматься все кубики, для этого немного изменил класс контроллера

```c#
public class PerceptronController : MonoBehaviour
{
    public Perceptron perceptron;

    [SerializeField] private List<GameObject> CubesToUp;

    private Color zeroColor;
    private Color oneColor;

    public Color ZeroColor => zeroColor;

    public Color OneColor => oneColor;

    void Awake()
    {
        zeroColor = Color.black;
        oneColor = Color.white;
    }

    public void UpAllCubes()
    {
        foreach (var cube in CubesToUp)
        {
            var position = cube.transform.position;
            position =
                new Vector3(position.x, position.y + 10, position.z);
            cube.transform.position = position;
        }
    }
}
```
Перетащил кубы для падения в список контроллера.
![image](https://user-images.githubusercontent.com/87923228/204027959-f2141063-3762-4ef1-ab2e-8ee0087f6983.png)

Вот наглядная демонстрация работы программы

https://user-images.githubusercontent.com/87923228/204029126-4c196477-9899-49d5-ae9c-1fc444a5092a.mp4

Давайте пройдемся по плюсам программы. Во первых это очень расширяемый код. Во вторых его удобно использовать. Кубы лежат списком, в инспекторе, хоть 1000 добавь, все будет работать, переключение перцептрона привязано к кнопке, тоже можно любой перцептрон добавить, цвета кубиков лежат в контроллере, поэтому можно отображать не только черный и белый, а изменить всего один раз там и он везде сразу изменится. Мне понравилось писать этот код, я отлично ознакомился с работой перцептрона и применил знания на практике.

## Выводы

Перцептрон это один из переходных инструментов в создании нейронных сетей, хотя название здесь не так уместно. У него есть слабые метса, которые исправили в нейронах. Мне очень понравилось работать с перцептроном, это очень хорошо помогает разобраться с тем, как работают нейронные сети и логика в целом.


| GitHub | [https://github.com/Saw4uk/DA-in-GameDev-lab1)] |
| Google Drive | [https://drive.google.com/drive/u/0/folders/1hHAR0jKNRbnM2ViSx3jJJBN7zfejcbe8] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
