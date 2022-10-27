# РАЗРАБОТКА СИСТЕМЫ МАШИННОГО ОБУЧЕНИЯ. [in GameDev]
Отчет по лабораторной работе #3 выполнил(а):
- Демина Анастасия Викторовна
- РИ210942
- Отметка о выполнении заданий (заполняется студентом):

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

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

## Цель работы
### Познакомиться с программными средствами для создания системы машинного обучения и ее интеграции в Unity.

## Задание 1
### Реализовать систему машинного обучения в связке Python - Google-Sheets – Unity.
Ход работы:
- Cоздала новый пустой 3D проект на Unity и добавила необходимые пакеты.
- Запустила Anaconda Prompt, скачала необходимые библиотеки(mlagents 0.28.0, torch 1.7.1), создала и активировала ML-агент.
- Создала на сцене в Unity плоскость, куб Target и сферу RollerAgent, объединила их в TargetArea. Изменила цвет сферы и куба, создав для них материалы.
- В скрипт-файл RollerAgent переписала код из маетериалов для лабораторной работы.
- Добавила компоненты Rigidbody, Decision Requester, Behavior Parameters к RollerAgent и настроила их так, как укзано в методических указаниях.
- В проект добавила файл конфигурации нейронной сети.
- Запустила сцену и проверила работу ML-Agent'а.
- Сделала 3, 9 и 27 копий модели и пронаблюдала за обучением модели.

![Image alt](https://github.com/cutterror/DA-in-GameDev-lab3/blob/main/images/27.png)

#### Запустив сцену с разными количествами копий моделей заметила, что с увелечением их количества скорочть обучения также увеличивается. В результате шар RollerAgent быстро достигает своей цели, не вываливаясь за гнаницы пола.


## Задание 2
### Подробно описать каждую строку файла конфигурации нейронной сети, найти информацию о компонентах Decision Requester, Behavior Parameters.
Ход работы:

behaviors:
  RollerBall: - Название модели
    trainer_type: ppo - Алгоритмы обучения модели (ppo, sac, poca)
    hyperparameters: 
      batch_size: 10 - Количество опытов в каждой итерации градиентного спуска
      
      - Количество опыта в каждой итерации. 
      - Т. е. сколько опыта должно быть собрано, прежде чем модель обучиться или обновиться.
      - Должно быть в несколько раз больше, чем `batch_size`. 
      - Обычно больший размер буфера соответствует более стабильным обновлениям обучения.
      buffer_size: 100
      
      - Начальная скорость обучения для градиентного спуска. 
      - Соответствует силе каждого шага обновления градиентного спуска. 
      - Если обучение нестабильно и вознаграждение постоянно не увеличивается, 
      - то это значение следует уменьшать.
      learning_rate: 3.0e-4
      
      - Сила энтропийной регуляризации, которая делает политику «более случайной». 
      - Это гарантирует, что агенты должным образом исследуют пространство действия во время обучения. 
      - Увеличение этого параметра обеспечит выполнение большего количества случайных действий. 
      - Необходимо, чтобы энтропия (измеряемая с помощью TensorBoard) 
      - медленно уменьшалась вместе с увеличением вознаграждения. 
      - Если энтропия падает слишком быстро, необходимо увеличить бета. 
      - Если энтропия падает слишком медленно, уменьшить.
      beta: 5.0e-4
      
      - Влияет на то, насколько быстро политика может развиваться во время обучения. 
      - Соответствует допустимому порогу расхождения между старой и новой политикой 
      - при обновлении градиентного спуска. 
      - Установка небольшого значения этого параметра приведет к более стабильным обновлениям, 
      - но также замедлит процесс обучения.
      epsilon: 0.2
      
      - Параметр регуляризации (лямбда) используется при расчете обобщенной оценки преимущества (GAE).
      - Можно рассматривать как то, насколько агент полагается на свою текущую оценку стоимости 
      - при вычислении обновленной оценки стоимости.
      - Низкие значения соответствуют большему полаганию на текущую оценку ценности 
      - (что может быть высоким смещением), 
      - а высокие значения соответствуют большему полаганию на фактические вознаграждения, 
      - полученные в среде (что может быть высокой дисперсией). 
      - Правильное значение может привести к более стабильному процессу обучения.
      lambd: 0.99
      
      - Количество проходов через буфер опыта при выполнении оптимизации градиентного спуска. 
      - Уменьшение этого параметра обеспечит более стабильные обновления за счет более медленного обучения.
      num_epoch: 3
      
      - Определяет, как скорость обучения изменяется с течением времени.
      - linear линейно уменьшает learning_rate, достигая 0 при max_steps.
      learning_rate_schedule: linear
      
    network_settings:
    
      - Применяется ли нормализация к входным данным векторного наблюдения. 
      - Эта нормализация основана на скользящем среднем и дисперсии векторного наблюдения. 
      - Нормализация может быть полезна в случаях со сложными задачами непрерывного управления, 
      - но может быть вредна для более простых задач дискретного управления.
      normalize: false
      
      - Количество блоков в скрытых слоях нейронной сети. 
      - Соответствуют количеству единиц в каждом полносвязном слое нейронной сети. 
      - Для простых задач, где правильное действие представляет собой простую комбинацию 
      - входных данных наблюдения, 
      - это значение должно быть небольшим. 
      - Для задач, где действие представляет собой очень сложное взаимодействие между переменными наблюдения, 
      - это значение должно быть большим.
      hidden_units: 128
      
      - Количество скрытых слоев в нейронной сети. 
      - Соответствует количеству скрытых слоев после ввода наблюдения или после кодирования 
      - CNN визуального наблюдения. 
      - Для простых задач меньше слоев, скорее всего, будут обучать быстрее и эффективнее. 
      - Для более сложных задач управления может потребоваться больше слоев.
      num_layers: 2
      
    reward_signals:
      extrinsic:
        
        - Коэффициент дисконтирования для будущих вознаграждений, поступающих из окружающей среды. 
        - Можно рассматривать как то, как далеко в будущем агент должен заботиться о возможных вознаграждениях. 
        - В ситуациях, когда агент должен действовать в настоящем, 
        - чтобы подготовиться к вознаграждению в отдаленном будущем, это значение должно быть большим. 
        - В случаях, когда вознаграждение является более быстрым, оно может быть меньше. 
        - Всегда должно быть строго меньше 1.
        gamma: 0.99
        
        - Фактор, на который умножается вознаграждение, данное средой. 
        - Типичные диапазоны будут варьироваться в зависимости от сигнала вознаграждения.
        strength: 1.0
        
    - Общее количество шагов - собранных наблюдений и предпринятых действий,которые 
    - необходимо выполнить в среде перед завершением процесса обучения. 
    - Если в среде есть несколько агентов с одинаковым именем поведения, все шаги, предпринятые 
    - этими агентами будут учитывать одно и то же значение max_steps.    
    max_steps: 500000
    
    - Сколько шагов опыта нужно собрать для каждого агента, прежде чем добавить его в буфер опыта.
    - Когда этот предел достигается до конца эпизода, оценка значения используется для прогнозирования 
    - общего ожидаемого вознаграждения из текущего состояния агента. 
    - Таким образом, этот параметр является компромиссом между менее предвзятой, 
    - но более высокой оценкой дисперсии (длинный временной горизонт) и более предвзятой, 
    - но менее разнообразной оценкой (короткий временной горизонт). 
    - В тех случаях, когда в эпизоде есть частые награды или эпизоды непомерно велики, 
    - более идеальным может быть меньшее количество. 
    - Это число должно быть достаточно большим, чтобы охватить все важные действия в последовательности 
    - действий агента.
    time_horizon: 64
    
    - Количество опытов, которое необходимо собрать перед созданием и отображением статистики обучения. 
    - Это определяет детализацию графиков в Tensorboard.
    summary_freq: 10000

## Задание 3
### Доработать сцену и обучите ML-Agent таким образом, чтобы шар перемещался между двумя кубами разного цвета. Кубы должны, как и в первом задании, случайно изменять координаты на плоскости. 
Ход работы:

Добавила новую цель ввиде куба на сцену с другим цветом. Дописала скрипт так, чтобы шар должен был достигнуть обе цели в любом порядке.

```py
using UnityEngine;
using Unity.MLAgents;
using Unity.MLAgents.Sensors;
using Unity.MLAgents.Actuators;
using Random = UnityEngine.Random;

public class RollerAgent : Agent
{
    Rigidbody rBody;
    // Start is called before the first frame update
    void Start()
    {
        rBody = GetComponent<Rigidbody>();
        Target = firstTarget;
    }

    public Transform firstTarget;
    public Transform secondTarget;

    private Transform Target;

    public override void OnEpisodeBegin()
    {
        if (this.transform.localPosition.y < 0)
        {
            this.rBody.angularVelocity = Vector3.zero;
            this.rBody.velocity = Vector3.zero;
            this.transform.localPosition = new Vector3(0, 0.5f, 0);
        }

        if (Target == firstTarget)
        {
            Target = secondTarget;
        }
        else
        {
            Target = firstTarget;
            secondTarget.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
            firstTarget.localPosition = new Vector3(Random.value * 8 - 4, 0.5f, Random.value * 8 - 4);
        }
    }

    public override void CollectObservations(VectorSensor sensor)
    {
        sensor.AddObservation(Target.localPosition);
        sensor.AddObservation(this.transform.localPosition);
        sensor.AddObservation(rBody.velocity.x);
        sensor.AddObservation(rBody.velocity.z);
    }

    public float forceMultiplier = 10;

    public override void OnActionReceived(ActionBuffers actionBuffers)
    {
        Vector3 controlSignal = Vector3.zero;
        controlSignal.x = actionBuffers.ContinuousActions[0];
        controlSignal.z = actionBuffers.ContinuousActions[1];
        rBody.AddForce(controlSignal * forceMultiplier);

        float distanceToTarget = Vector3.Distance(transform.localPosition, Target.localPosition);

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

![Alt Text]([https://media.giphy.com/media/vFKqnCdLPNOKc/giphy.gif](https://github.com/cutterror/DA-in-GameDev-lab3/blob/main/images/2022-10-27-18-55-40.gif))


## Выводы
  Игровой баланс - это равновесие между удовольствием от игры и сложности в ней. Или же, касательно многопользовательских игр, - это равновесие в возможностях игроков. В общем-то, игровой баланс касается всех игровых объектов и напрямую влияет на впечатления от игрового процесса.
  
  Системы машинного обучения могут быть использованы для корректирования игрового баланса самыми различными способами. Мы можем симулировать поведение игроков, которые так же обучаются со временем, проведенным в игре, собирать и анализировать статистику, на основе которой можно будет сделать выводы о сложности уровней и прочем.



| Plugin | README |
| ------ | ------ |
| Dropbox | [plugins/dropbox/README.md][PlDb] |
| GitHub | [plugins/github/README.md][PlGh] |
| Google Drive | [plugins/googledrive/README.md][PlGd] |
| OneDrive | [plugins/onedrive/README.md][PlOd] |
| Medium | [plugins/medium/README.md][PlMe] |
| Google Analytics | [plugins/googleanalytics/README.md][PlGa] |

## Powered by

**BigDigital Team: Denisov | Fadeev | Panov**
