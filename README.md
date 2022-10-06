# АНАЛИЗ ДАННЫХ И ИСКУССТВЕННЫЙ ИНТЕЛЛЕКТ [in GameDev] 
Отчет по лабораторной работе #2 выполнил(а):
- Ермолинский Семён Михайлович
- РИ000024 

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

[![N|Solid](https://cldup.com/dTxpPi9lDf.thumb.png)](https://nodesource.com/products/nsolid)

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

Структура отчета

- Данные о работе: название работы, фио, группа, выполненные задания.
- Цель работы.
- Задание 1.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 2.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Задание 3.
- Код реализации выполнения задания. Визуализация результатов выполнения (если применимо).
- Выводы.
- ✨Magic ✨

## Цель работы
Знакомства с программными средствами для организции передачи данных между Google Cloud, Sheets, Python и Unity.

## Задание 1
### Реализовать совместную работу и передачу данных в связке Python - Google-Cloud-Sheets – Unity.
Ход работы:
- Шаг 1. В консоли Google Cloud подключил необходимые API сервисов (Drive & Sheets), создал сервисный аккаунт, создал API-ключ, выгрузил ключ сервисного аккаунта

![Снимок экрана 2022-10-05 в 21 26 42](https://user-images.githubusercontent.com/100123572/194123437-8596398f-f49e-4cba-bec9-2f868e4cfac4.png)

- Шаг 2. Реализовать запись данных из скрипта на Python в Google Spreadsheets. Данные описывают изменение темпа инфляции на протяжении 11 отсчётных периодов, с учётом стоимости игрового объекта в каждый период.

![Снимок экрана 2022-10-05 в 21 27 53](https://user-images.githubusercontent.com/100123572/194123691-f22c370d-d7e0-4f03-a8fd-32925858c8e3.png)


- Шаг 3-4. Создать новый проект на Unity, который будет получать данные из Google Spreadsheets, в которую были записаны данные в предыдущем пункте. Написать функционал на Unity, в котором будет воспризводиться аудиофайл в зависимости от значения данных из таблицы.

![Снимок экрана 2022-10-05 в 21 30 37](https://user-images.githubusercontent.com/100123572/194124291-6710c742-86f8-4132-b12d-c72d64b24efd.png)

## Задание 2
### Необходимо связать данные из ленейной регрессии с кодом и вывести loss в таблицу

Вкладываю код с функцией loss из прошлой лабараторной работы в код Задания 1 лабараторной работы 2.

import gspread
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


def optimize(a, b, x, y):
    num = len(x)
    prediction = model(a, b, x)
    da = (1.0 / num) * ((prediction - y) * x).sum()
    db = (1.0 / num) * ((prediction - y).sum())
    a = a - Lr * da
    b = b - Lr * db
    return a, b


def iterate(a, b, x, y, times):
    for i in range(times):
        a, b = optimize(a, b, x, y)
    return a, b


a = np.random.rand(1)
b = np.random.rand(1)
Lr = 0.000001

gc = gspread.service_account(filename='unitydatasciencelab2-a9a124bf1c1b.json')
sh = gc.open('Unitysheetlab2')
price = np.random.randint(2000, 10000, 11)
mon = list(range(1, 11))
i = 0
while i <= len(mon):
    i += 1
    if i == 0:
        continue
    else:
        a, b = iterate(a, b, x, y, 100)
        prediction = model(a, b, x)
        loss = loss_function(a, b, x, y)
        tempInf = loss
        tempInf = str(tempInf)
        tempInf = tempInf.replace('.', ',')
        sh.sheet1.update(('A' + str(i)), str(i))
        sh.sheet1.update(('B' + str(i)), str(tempInf))

        print(tempInf)
        
Проверяем работу с соответствующей таблицей

![Снимок экрана 2022-10-06 в 20 38 09](https://user-images.githubusercontent.com/100123572/194370020-e572e194-e34f-4647-8bd8-482b9ed10f7f.png)

![Снимок экрана 2022-10-06 в 20 39 12](https://user-images.githubusercontent.com/100123572/194370099-aa6b8dbc-5183-40ab-8ea1-0b711834993f.png)


## Задание 3
### Подсоединить данные из loss с unity

Изменяем диапазон данных в коде, чтобы одни подходили под диапазон loss.

Изменяем количество колонок в соответствии с новыми данными.

using System.Collections;
using System.Collections.Generic;
using UnityEngine;
using UnityEngine.Networking;
using SimpleJSON;

public class NewBehaviourScript : MonoBehaviour
{
    public AudioClip goodSpeak;
    public AudioClip normalSpeak;
    public AudioClip badSpeak;
    private AudioSource selectAudio;
    private Dictionary<string,float> dataSet = new Dictionary<string, float>();
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
        if (dataSet["Mon_" + i.ToString()] <= 300 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioGood());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] > 300 & dataSet["Mon_" + i.ToString()] < 1000 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioNormal());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }

        if (dataSet["Mon_" + i.ToString()] >= 1000 & statusStart == false & i != dataSet.Count)
        {
            StartCoroutine(PlaySelectAudioBad());
            Debug.Log(dataSet["Mon_" + i.ToString()]);
        }
    }

    IEnumerator GoogleSheets()
    {
        UnityWebRequest curentResp = UnityWebRequest.Get("https://sheets.googleapis.com/v4/spreadsheets/1lZxJ3skXCI_8SYFKXjE1whgoqXU72sgwurgToN296lk/values/Лист1?key=AIzaSyAegSwvN1SRrzKZqgKkAWT7x6TOQCHPs4I");
        yield return curentResp.SendWebRequest();
        string rawResp = curentResp.downloadHandler.text;
        var rawJson = JSON.Parse(rawResp);
        foreach (var itemRawJson in rawJson["values"])
        {
            var parseJson = JSON.Parse(itemRawJson.ToString());
            var selectRow = parseJson[0].AsStringList;
            dataSet.Add(("Mon_" + selectRow[0]), float.Parse(selectRow[1]));
        }
    }

    IEnumerator PlaySelectAudioGood()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = goodSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioNormal()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = normalSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(3);
        statusStart = false;
        i++;
    }
    IEnumerator PlaySelectAudioBad()
    {
        statusStart = true;
        selectAudio = GetComponent<AudioSource>();
        selectAudio.clip = badSpeak;
        selectAudio.Play();
        yield return new WaitForSeconds(4);
        statusStart = false;
        i++;
    }
}



## Выводы

Повторил то, что было в безмолвном видео, но понял как это работает. Повторить по видео опять смогу, но и сам научился и что делаю знаю. Обучение по видео я воспринимаю отлично, и без звука лучше. Данный уровень программирования понимаю. Если есть непонимание, то либо что конкретно изучать в связи со своим непониманием знаю, либо разбираюсь сам, пока не пойму, сколько бы это времени не заняло. Смогу интегрировать в дальнейшем данный код и если его потеряю, то всегда смогу обратиться к материалам курса. Основы изучаю сам.   

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
