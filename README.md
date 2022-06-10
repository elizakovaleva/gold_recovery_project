# gold_recovery_project

## Постановка задачи

Модель должна предсказать коэффициент восстановления золота из золотосодержащей руды. Представлены данные с параметрами добычи и очистки.

Модель поможет оптимизировать производство, чтобы не запускать предприятие с убыточными характеристиками.

Этапы:

Подготовка данных;

Исследовательский анализ данных;

Моделирование.

## Технологический процесс
Когда добытая руда проходит первичную обработку, получается дроблёная смесь. Её отправляют на флотацию (обогащение) и двухэтапную очистку.
![image](https://user-images.githubusercontent.com/44114329/173049062-73856a75-d78b-439c-93f7-b8fde1668127.png)

Опишем каждую стадию:
1. Флотация

Во флотационную установку подаётся смесь золотосодержащей руды. После обогащения получается черновой концентрат и «отвальные хвосты», то есть остатки продукта с низкой концентрацией ценных металлов.
На стабильность этого процесса влияет непостоянное и неоптимальное физико-химическое состояние флотационной пульпы (смеси твёрдых частиц и жидкости).
2. Очистка

Черновой концентрат проходит две очистки. На выходе получается финальный концентрат и новые отвальные хвосты.

## Описание данных

### Технологический процесс

Rougher feed — исходное сырье

Rougher additions (или reagent additions) — флотационные реагенты: Xanthate, Sulphate, Depressant

Xanthate **— ксантогенат (промотер, или активатор флотации);

Sulphate — сульфат (на данном производстве сульфид натрия);

Depressant — депрессант (силикат натрия).

Rougher process (англ. «грубый процесс») — флотация

Rougher tails — отвальные хвосты

Float banks — флотационная установка

Cleaner process — очистка

Rougher Au — черновой концентрат золота

Final Au — финальный концентрат золота

### Параметры этапов

air amount — объём воздуха

fluid levels — уровень жидкости

feed size — размер гранул сырья

feed rate — скорость подачи

### Наименование признаков

Наименование признаков должно быть такое:
[этап].[тип_параметра].[название_параметра]

Пример: rougher.input.feed_ag

Возможные значения для блока [этап]:

rougher — флотация

primary_cleaner — первичная очистка

secondary_cleaner — вторичная очистка

final — финальные характеристики

Возможные значения для блока [тип_параметра]:

input — параметры сырья

output — параметры продукта

state — параметры, характеризующие текущее состояние этапа

calculation — расчётные характеристики

## Расчёт эффективности

Нужно смоделировать процесс восстановления золота из золотосодержащей руды.

Эффективность обогащения рассчитывается по формуле
![image](https://user-images.githubusercontent.com/44114329/173049986-99a1b1e1-19a3-4665-b824-0e81e334d7c7.png)
где:
C — доля золота в концентрате после флотации/очистки;
F — доля золота в сырье/концентрате до флотации/очистки;
T — доля золота в отвальных хвостах после флотации/очистки.

Для прогноза коэффициента нужно найти долю золота в концентратах и хвостах. Причём важен не только финальный продукт, но и черновой концентрат.

## Метрика качества

Для решения задачи введём новую метрику качества — sMAPE (англ. Symmetric Mean Absolute Percentage Error, «симметричное среднее абсолютное процентное отклонение»).
Она похожа на MAE, но выражается не в абсолютных величинах, а в относительных. Почему симметричная? Она одинаково учитывает масштаб и целевого признака, и предсказания.

Метрика sMAPE вычисляется так:
![image](https://user-images.githubusercontent.com/44114329/173050194-ce13321e-3bf2-44a1-a4af-93e1dae31702.png)
Обозначения:
![image](https://user-images.githubusercontent.com/44114329/173050431-ed178832-a741-499b-a1e4-802d923e3321.png)
Значение целевого признака для объекта с порядковым номером i в выборке, на которой измеряется качество.
![image](https://user-images.githubusercontent.com/44114329/173050454-1e5e2e2d-563c-4bcb-8dd9-41bd1b99128b.png)
Значение предсказания для объекта с порядковым номером i, например, в тестовой выборке.
![image](https://user-images.githubusercontent.com/44114329/173050505-ac71ca3c-f540-4256-a797-6f5f19f50919.png)
Количество объектов в выборке.
![image](https://user-images.githubusercontent.com/44114329/173050528-7452d779-5fb1-41f5-a54a-c2bf0a8f54f9.png)
Суммирование по всем объектам выборки (i меняется от 1 до N).

Нужно спрогнозировать сразу две величины:

эффективность обогащения чернового концентрата rougher.output.recovery;

эффективность обогащения финального концентрата final.output.recovery.

Итоговая метрика складывается из двух величин:
![image](https://user-images.githubusercontent.com/44114329/173050575-381a9ac1-2930-4ab2-89e3-3d01dfa3b57c.png)

Данные находятся в трёх файлах:
gold_recovery_train_new.csv — обучающая выборка;
gold_recovery_test_new.csv — тестовая выборка;
gold_recovery_full_new.csv — исходные данные.
Данные индексируются датой и временем получения информации (признак date). Соседние по времени параметры часто похожи.
Некоторые параметры недоступны, потому что замеряются и/или рассчитываются значительно позже. Из-за этого в тестовой выборке отсутствуют некоторые признаки, которые могут быть в обучающей. Также в тестовом наборе нет целевых признаков.
Исходный датасет содержит обучающую и тестовую выборки со всеми признаками.

## Выполнение проекта
1. Подготовка данных

1.1. Изучение файлов.

Путь к файлам:
/datasets/gold_recovery_train_new.csv. 
/datasets/gold_recovery_test_new.csv. 
/datasets/gold_recovery_full_new.csv. 

1.2. Проверим, что эффективность обогащения рассчитана правильно. Вычислим её на обучающей выборке для признака rougher.output.recovery. Найдем MAE между нашими расчётами и значением признака.

1.3. Проанализируем признаки, недоступные в тестовой выборке. Что это за параметры? К какому типу относятся?

1.4. Проведем предобработку данных.

2. Проанализируем данные

2.1. Посмотрим, как меняется концентрация металлов (Au, Ag, Pb) на различных этапах очистки.

2.2. Сравним распределения размеров гранул сырья на обучающей и тестовой выборках. Если распределения сильно отличаются друг от друга, оценка модели будет неправильной.

2.3. Исследуем суммарную концентрацию всех веществ на разных стадиях: в сырье, в черновом и финальном концентратах.

3. Построим модель

3.1. Напишем функцию для вычисления итоговой sMAPE.

3.2. Обучим разные модели и оценим их качество кросс-валидацией. Лучшую модель проверим на тестовой выборке.

Формулы метрик качества:
![image](https://user-images.githubusercontent.com/44114329/173052008-33c05ff5-eaa7-49de-b3b6-6fcecaa40c24.png)
![image](https://user-images.githubusercontent.com/44114329/173052033-cfadffa4-73f0-40fd-89d8-0ffb45add7a2.png)

