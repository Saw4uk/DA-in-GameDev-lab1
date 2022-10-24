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

## Цель работы
Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity

## Задание 1
### Реализовать систему обучения MLAgent
Сначала я создал новый проект в Unity.

![image](https://user-images.githubusercontent.com/87923228/197591976-4be3a37f-f6f5-4fb5-9870-517f4be5a18e.png)

Затем я скачал папку с MlAgent в облаке с исходными данными.

![image](https://user-images.githubusercontent.com/87923228/197592012-42ebcbef-edea-4b24-bd78-e61c77e8872c.png)

В созданный проект я добавл MlAgent через PackageManager. 

![image](https://user-images.githubusercontent.com/87923228/197592195-23dc93d7-773b-412d-b798-f2027cff8097.png)

Далее я провел все необходимые манипуляции с Анакондой (Я её случайно закрыл в процессе работы, поэтому часть с установкой torch и других библиотек не сохранилась)
![image](https://user-images.githubusercontent.com/87923228/197592215-5cb94867-5d8f-423e-b630-52309ec2dfc3.png)

Все введенные команды необходимы были для установки Агента на мою машину, для того, чтобы я смог в юнити проекте взаимодействовать с ним. Для этого я в Юнити создал тестовую сцену, куда разместил агента и цель.

![image](https://user-images.githubusercontent.com/87923228/197592396-8d441ec0-35b8-4fcb-aef2-2318e332bbaf.png)

В файл скрипта агентя я построчно переписал код, чтобы лучше разобраться в том, как он работает.
```c#
 Rigidbody rBody;
    public Transform Target;
    public float forceMultiplier = 10;
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }
    
    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Target.localPosition = new Vector3(Random.value * 8-4, 0.5f, Random.value * 8-4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }
  
    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, Target.localPosition);

        if(distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
```

Обьекту сфера добавил компонент Rigitbody. Как я понял из исходного кода, это необходимо для того, чтобы отслеживать соприкосновение с коллайдером куба.

![image](https://user-images.githubusercontent.com/87923228/197592551-05cdfa5d-b46e-4ad8-8abc-2a1be64aab9e.png)

Моё предположение подтвердилось, когда я выключил коллайдер у куба. Компонент Behavior Parameters добавился автоматически, а Decision Requester пришлось добавлять вручную. Я задал всем компонентам необходимые характеристики и приступил к настройке конфигурации нейронной сети. Сначала я добавил файл конфигурации в проект

![image](https://user-images.githubusercontent.com/87923228/197592634-05ed4639-f94f-48a3-9de5-951926d89098.png)

Затем приступилк тесту, введя в консоль анаконды следующие команды, но ничего не получилось. Экспериментальная версия MlAgent не работала и мне пришлось сначала обновить её до рабочей release версии, а потом запускать тесты

![image](https://user-images.githubusercontent.com/87923228/197592686-46b244ef-2153-470f-a8b5-f3dd2654c06f.png)

В задании написано создать 3 9 и 27 копий, но я создал 2 4 и 16 копий.

![image](https://user-images.githubusercontent.com/87923228/197592715-4d78426a-22cc-4fe6-83b9-1dc9256bae61.png)

После достаточно длительного обучения я заметил результаты. Во первых во всех случаях рано или поздно шарик начинает в подавляющем большинтсве случаев достигать кубика, но время от начала обучения до этого момента завсисит от числа TargetArea. Чем их больше, тем эффективнее обучение. Исходя из этого можно сделать вывод, что если вам необходимо увеличить скорость обучения нейронной сети, то можно увеличить количество итераций. Так же, понаблюдав за обучением нельзя не отметить эффективность этой модели. По прошествию относительно небольшого промежутка времени уже почти каждый шарик достигал кубика.

## Задание 2
### Подробно опишите каждую строку файла конфигурации нейронной сети, доступного в папке с файлами проекта по ссылке.
По ссылке файл недоступен, но я нашел решение. Судя по работе нейронной сети, файл конфигурации - это rollerball_config.yaml. Ну во первых в нем написано config, что недвусмысленно намекает нам на то, что он настраивает что-то, а судя по расширению настраивает MlAgent, а значит он является конфигурационным файлом нейрнной сети. Там я нашел следующий код
```
behaviors:
  RollerBall:
    trainer_type: ppo
    hyperparameters:
      batch_size: 10
      buffer_size: 100
      learning_rate: 3.0e-4
      beta: 5.0e-4 
      epsilon: 0.2
      lambd: 0.9
      num_epoch: 3
      learning_rate_schedule: linear
    network_settings:
      normalize: false
      hidden_units: 128
      num_layers: 2
    reward_signals:
      extrinsic:
        gamma: 0.99
        strength: 1.0
    max_steps: 500000 
    time_horizon: 64
    summary_freq: 10000
```
Разобравшись в коде и изрядно погуглив я так и не смог найти однозначного ответа на то, какие строчки за что отвечают. Я разобрался с тем, зачем нужны файл с расширением Yaml. Они используются для настройки различных самообучамых систем. Применяется в том числе и в яндексе. Я понял, что hyperparameters - это настройки присущие всей сети, network_settings - это настройки связи, а reward_signals за обратную связь и вывод результата. max_steps - максимальное число итераций, а summary_freq отвечает за сбор промежуточного результата.


## Задание 3
### Доработайте сцену и обучите ML-Agent таким образом, чтобы шар перемещался между двумя кубами разного цвета. Кубы должны, как и в первом задании, случайно изменять координаты на плоскости. 

Сначала я добавил на Target Area второй куб, для того, чтобы MlAgent мог перемсещаться между ним и основным кубом.
![image](https://user-images.githubusercontent.com/87923228/197587273-84f7f284-dcd5-4949-98e3-6de9981b5f9d.png)
Мне пришлось слегка доработать скрипт Агента. Вместо изменения положения таргета он будет менять положения кубов
```c#
public class RollerAgent : Agent
{
    Rigidbody rBody;
    public Transform RealTarget;
    public Transform Cube1;
    public Transform Cube2;
    public float forceMultiplier = 10;
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        Cube1.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
        Cube2.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(RealTarget.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }

    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, RealTarget.localPosition);

        if (distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}
```
Теперь я передаю агенту и кубы и цель, а у цели меняется трансформ, в случае если трансформ кубов изменился. Я решил сделать это через метод Update.
```c#
public class RealTarget : MonoBehaviour
{
    [SerializeField]
    private Transform firstCube;
    [SerializeField]
    private Transform secondCube;

    private void Update()
    {
        var fTr = firstCube.transform.position;
        var sTr = secondCube.transform.position;
        transform.position = new Vector3((fTr.x + sTr.x)/2, (fTr.y + sTr.y) / 2, (fTr.z + sTr.z) / 2);
    }
```
Безусловно, этот код не идеален и можно, например изменить его таким образом, чтобы рассчет положения происходил только после изменения положения кубов, а не постоянно. 
```c#
public class RealTarget : MonoBehaviour
{
    [SerializeField]
    private Transform firstCube;
    [SerializeField]
    private Transform secondCube;

    public void UpdateTransform()
    {
        var fTr = firstCube.transform.position;
        var sTr = secondCube.transform.position;
        transform.position = new Vector3((fTr.x + sTr.x)/2, (fTr.y + sTr.y) / 2, (fTr.z + sTr.z) / 2);
    }
}
```
Тогда можно написать метод, который будет вызываться извне. Напрмиер из класса Агента
```c#
public class RollerAgent : Agent
{
    Rigidbody rBody;
    public GameObject RealTarget;
    public Transform Cube1;
    public Transform Cube2;
    public float forceMultiplier = 10;
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
    }

    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }
        RealTarget.GetComponent<RealTarget>().UpdateTransform();
        Cube1.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
        Cube2.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
    }
    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(RealTarget.transform.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }

    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(this.transform.localPosition, RealTarget.transform.localPosition);

        if (distanceToTarget < 1.42f)
        {
            SetReward(1.0f);
            EndEpisode();
        }
        else if (this.transform.localPosition.y < 0)
        {
            EndEpisode();
        }
    }
}
```

В таком случае обучение будет проходить быстрее.

Как вы можете видеть из следующего ролика Шарик действительно движется к точке между кубами


https://user-images.githubusercontent.com/87923228/197588934-e4d7076d-7f44-40e8-92b5-a92a3a656929.mp4



## Выводы

Что такое игровой баланс? Игровой баланс отражает то, как ощущается игра. Это совокупность всех возможностей, которые она предоставляет игроку на том или ином этапе. Игрвой баланс одна из самых важных частей игры и сейчас я опишу, как с помощью систем машинного обучения можно в игре про выживание настроить баланс выпадения вещей. Допустим вы разрабатываете игру-сурвайвал, в которой выжившие с каждым днем все меньше оставляют вещей тем, кто после них придет лутать локацию. Тут на лицо алгоритм линейной регрессии. Но вы не хотите слепого уменьшения количества лута. По мере течения времени число целых вещей уменьшается, а сломанных увеличивается. Сломанные вещи можно разобрать и из них сделать компоненты, из которых собрать что-то смаодельное. Возмем, напрмер, автомат. Вы хотите, чтобы через один игровой год автоматов заводского производства было в два раза больше, потому что люди за год не успели наладить производство, через 4 года их было поравну, а через 8 лет, почти все автоматы были бы самодельные. Вы создаете симуляцию, в котором шанс спавна автомата уменьшается пропорционально количеству найденных автоматов и регулируете его в зависимости от результата машинного обучения. Если в результате новые автоматы не исчезают, значит шанс нужно уменьшать сильнее, а если исчезают за первый год, то шанс увеличивать. Схема, я думаю, ясна. Системы машинного обучения позволят нам узнать, как поведет себя система при тех или иных исходных данных. Регулируя эти исходные данные, мы можем отбалансировать нужный нам аспект игры должным образом.
 

| GitHub | [https://github.com/Saw4uk/DA-in-GameDev-lab1)] |
| Google Drive | [https://drive.google.com/drive/u/0/folders/1hHAR0jKNRbnM2ViSx3jJJBN7zfejcbe8] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
