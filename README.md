# СБОР, ОБРАБОТКА И ВИЗУАЛИЗАЦИЯ ТЕСТОВОГО НАБОРА ДАННЫХ. [in GameDev]
Отчет по лабораторной работе #2 выполнил(а):
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
Ознакомиться с основными операторами зыка Python на примере реализации линейной регрессии.

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity.
Ход работы:
- В облачном сервисе google console подключила API для работы с google sheets и google drive.
- Реализовала запись данных из скрипта на python в google-таблицу. Данные описывают изменение темпа инфляции на протяжении 11 отсчётных периодов, с учётом стоимости игрового объекта в каждый период.

Скрипт:

```py
import gspread
import numpy as np

gc = gspread.service_account(filename='unitydatasciense-365205-27e8e9c05cbd.json')
sheet = gc.open('UnityData')
price = np.random.randint(2000, 10000, 11)
mon = list(range(1, 11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        tempInf = ((price[i - 1] - price[i - 2])/price[i - 2])*100
        tempInf = str(tempInf)
        tempInf = tempInf.replace('.', ',')
        sheet.sheet1.update(('A' + str(i)), str(i))
        sheet.sheet1.update(('B' + str(i)), str(price[i-1]))
        sheet.sheet1.update(('C' + str(i)), str(tempInf))
        print(tempInf)

```
Записанные в таблицу данные:

| Период | Цена объекта | Инфляция |
| ------ | ------ | ------ |
| 1  | 6384 | 34,7972972972973    |
| 2  | 4546 | -28,790726817042607 |
| 3  | 6953 | 52,9476462824461    |
| 4  | 3325 | -52,178915576010354 |
| 5  | 2973 | -10,586466165413535 |
| 6  | 3732 | 25,529767911200807  |
| 7  | 3590 | -3,804930332261522  |
| 8  | 3211 | -10,557103064066853 |
| 9  | 6467 | 101,40143257552165  |
| 10 | 8825 | 36,46203803927632   |
| 11 | 4736 | -46,334277620396605 |

- Создала новый проект на Unity, который будет получать данные из google-таблицы, в которую были записаны данные в предыдущем пункте.
- Написала функционал на Unity, в котором будет воспризводиться аудио-файл в зависимости от значения данных из таблицы:

```py

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
    private Dictionary<string, float> dataSet = new Dictionary<string, float>();
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
        if (dataSet["Mon_" + i.ToString()] <= 10 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioMode(GoodSpeak));
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 10 & dataSet["Mon_" + i.ToString()] < 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioMode(NormalSpeak));
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 100 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioMode(BadSpeak));
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1Iji8SOdY2JcdGTF19Nd0O2aQLkdRdeoAxntz2xZPICE/values/Лист1?key=AIzaSyAxo_3QPnebOrbOnnwIJxtKjy83Wz9t4lE");
        yield return curentResp.SendWebRequest();
        string RawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(RawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioMode(AudioClip mode)
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = mode;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}

```

## Задание 2
### Реализовать запись в Google-таблицу набора данных, полученных с помощью линейной регрессии из лабораторной работы № 1. 
Ход работы:

```py

import gspread
import numpy as np
import matplotlib.pyplot as plt

gc = gspread.service_account(filename='unitydatasciense-365205-27e8e9c05cbd.json')
sh = gc.open('UnityData')
priceX = np.random.randint(2000, 10000, 11)
priceX = np.array(priceX)
priceY = np.random.randint(2000, 10000, 11)
priceY = np.array(priceY)
mon = list(range(1, 11))


def model(a, b, x):
  return a * x + b


def loss_function(a, b, x, y):
  num = len(x)
  prediction = model(a, b, x)
  return (0.5 / num) * (np.square(prediction - y)).sum()


def optimize(a, b, x, y):
  num = len(x)
  prediction = model(a, b, x)
  da = (1.0 / num) * ((prediction - y) * x).sum()
  db = (1.0 / num) * (prediction - y).sum()
  a = a - Lr * da
  b = b - Lr * db
  return a, b


def iterate (a, b, x, y, times):
  for i in range(times):
    a, b = optimize(a, b, x, y)
  return a, b


x = [2, 11, 13, 16, 25, 36, 49, 56, 76, 84, 99]
x = np.array(x)
y = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11]
y = np.array(y)

a = np.random.rand(1)
b = np.random.rand(1)
Lr = 0.000001

a, b = iterate(a, b, priceX, priceY, 1)
prediction = model(a, b, priceX)
loss = loss_function(a, b, priceX, priceY)
print(a, b, loss)
plt.scatter(x, y)
plt.plot(x, prediction)


i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        tempInf = ((prediction[i-1]-prediction[i-2])/prediction[i-2])*100
        tempInf = str(tempInf)
        tempInf = tempInf.replace('.', ',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(prediction[i-1]//1))
        sh.sheet1.update(('C' + str(i)), str(tempInf))
        print(prediction)

```

Записанные в таблицу значения:

| Период | Цена объекта | Инфляция |
| ------ | ------ | ------ |
| 1	| 21816.0 |	-26,513670681033197 |
| 2	| 29489.0 | 35,172294080543345 |
| 3 | 51353.0 |74,14052223136137 |
| 4 | 46998.0	| -8,481097991263939 |
| 5 | 57158.0	| 21,617489013368363 |
| 6 | 48582.0	| -15,004136784050331 |
| 7 | 76108.0	| 56,65843010887136 |
| 8 | 33576.0	| -55,88382852786492 |
| 9 | 77058.0	| 129,50443057692317 |
| 10 | 33591.0 | -56,407303573396504 |
| 11 | 29687.0 | -11,621850783293453 |


## Задание 3
### Самостоятельно разработать сценарий воспроизведения звукового сопровождения в Unity в зависимости от изменения считанных данных в задании 2.
Ход работы:

```py

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
    private Dictionary<string, float> dataSet = new Dictionary<string, float>();
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
        if (CheckInflation(int.MinValue, 10))
        {
            StartCoroutine(PlaySelectAudioMode(GoodSpeak));
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (CheckInflation(10, 100))
        {
            StartCoroutine(PlaySelectAudioMode(NormalSpeak));
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (CheckInflation(100, int.MaxValue))
        {
            StartCoroutine(PlaySelectAudioMode(BadSpeak));
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    bool CheckInflation(int min, int max)
    {
        var value = dataSet["Mon_" + i.ToString()];
        return value >= min & value <= max & statusStart == false & i != dataSet.Count;
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1Iji8SOdY2JcdGTF19Nd0O2aQLkdRdeoAxntz2xZPICE/values/Лист1?key=AIzaSyAxo_3QPnebOrbOnnwIJxtKjy83Wz9t4lE");
        yield return curentResp.SendWebRequest();
        string RawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(RawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[2]));
        }
    }

    IEnumerator PlaySelectAudioMode(AudioClip mode)
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = mode;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}

```

## Выводы

В результате проделанной лабораторной работы узнала, как реализовать совместную работу и передачу данных в связке Python - Google-Sheets – Unity: писать скрипт на Python для заполнения Google-таблиц, получать, обрабатывать данные из Google-Sheets в Unity через скрипт на C#. Смоделировала изменение цены на товар и инфляцию, исходя из разницы цен в разные промежутки итерации.

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
