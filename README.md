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

### Запустив сцену с разными количествами копий моделей заметила, что с увелечением их количества скорочть обучения увеличивается.


## Выводы



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
