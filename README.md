# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev]
Отчет по лабораторной работе #4 выполнил(а):
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


## Выводы

Исходя из проделанной работы можно сделать вывод. Во первых - эффективность самообучаемого алгоритма растет по мере увеличения количества итераций.  Во вторых самообучаемый алгоритм позволяет достичь высокой точности рассчетов при прогнозах. Это очень полезно для симуляции экономики, например.


| GitHub | [https://github.com/Saw4uk/DA-in-GameDev-lab1)] |
| Google Drive | [https://drive.google.com/drive/u/0/folders/1hHAR0jKNRbnM2ViSx3jJJBN7zfejcbe8] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
