# Создание бенчмарка для задач фрагментации горных пород

![Бенчмарк для задач фрагментации горных пород](https://github.com/AAEfimov/Rock_fragmentation_benchmark/blob/main/Rocks.jpg)

## Цель и задачи проекта

Цель проекта: Создание бенчмарка\* для задачи фрагментации горных пород. Под задачей фрагментации горных пород подразумевается определение распределения размера фрагментов после проведения взрывных работ.

\* Бенчмарк – это набор данных достаточно большого объема, собранный в широком диапазоне условий, и понятный бейзлайн к нему.

Были поставлены следующие задачи:
1. Сбор изображений фрагментов горных пород в условиях открытого карьера из сети, в том числе датасетов и отдельных изображений
2. Помощь в разметке данных
3. Участие в формировании бейзлайна

## Актуальность работы

Как известно, у геологов очень часто возникает задача оценки числа фрагментов горной породы и их размеров (задача фрагментации). 

Уже несколько лет популярны подходы к решению таких задач на основе систем компьютерного зрения с использованием нейронных сетей семантической или экземплярной сегментации. Очевидно, что для их работы требуется привлечение мощных компьютерных ресурсов. 

В связи с этим использование альтернатив – алгоритмов быстрого обнаружения объектов на цифровых изображениях горной породы является очень актуальным.


## Начальные параметры проекта

Результат(продукт): Репозиторий с открытым доступом

Критерии приемки результата/ продукта: Собрано и размечено не менее 2000 изображений фрагментов горных пород в условиях открытого карьера.

Описание проекта: Помощь в создании бенчмарка для задачи фрагментации горных пород, то есть в определении распределения размеров фрагментов горных пород в условиях открытого карьера. Примеры данных были нам предоставлены.

## Идея генерации датасета

С учётом сложности сбора датасета из открытых источников было предложено создать датасет с различными условиями съемки и качеством пород из сгенерированных изображений

## Генерация изображений из текста

Для генерации изображений использовалась модель Stable Diffusion 3.5 Large

1. Photograph of a coal pit with numerous rocks located 10-15 meters from the camera, against a clear blue sky
2. Photograph of a granite quarry with numerous rocks located 10-15 meters from the camera, against a clear blue sky
3. Photograph of an iron ore quarry with numerous rocks located 10-15 meters from the camera, against a clear blue sky
4. A quarry landscape image of a coal pit with large scattered stones located 10-15 meters in front of the photographer
5. A quarry landscape image a granite quarry with large scattered stones located 10-15 meters in front of the photographer
6. A quarry landscape image of an iron ore quarry with large scattered stones located 10-15 meters in front of the photographer
7. Close-up shot of a coal quarry focusing on various stones located 10-15 meters from the lens, surrounded by rough terrain
8. Close-up shot of a granite quarry focusing on various stones located 10-15 meters from the lens, surrounded by rough terrain
9. Close-up shot of a granite quarry focusing on various stones located 10-15 meters from the lens, surrounded by rough terrain
10. Close-up shot of an iron ore quarry focusing on various stones located 10-15 meters from the lens, surrounded by rough terrain

## Порядок обучения модели. Её параметры

Для обучения модели сегментации и детекции использовалась современная предобученная модель Mask R-CNN X-101-32x8d FPN 3x из библиотеки Detectron2. Эта модель основана на архитектуре Mask R-CNN, которая сочетает в себе объектную детекцию и сегментацию, а также использует ResNeXt-101 в качестве backbone с групповой сверткой(32x8d) и Feature Pyramid Network(FPN) для более эффективного обнаружения объектов на разных масштабах.

Указанная модель из библиотеки Detectron2 была дообучена в следующем порядке:
Шаг 1. дообучение на полностью сгенерированном датасете
Шаг 2. дообучение модели из шага 1 на реальных размеченных фотографиях  
Шаг 3. дообучение на реальных размеченных фотографиях - улучшение качества модели на реальном датасете

Проведено 12 вариантов дообучения модели:
1 вариант: на полностью сгенерированном датасете
2 – 6 варианты: Kfold
7 вариант: дообучение на реальных данных
8-12 варианты: дообучение каждого K-Flod на реальных данных

При этом каждая обученная модель тестировалась на тестовой подвыборке из реальных данных (на одной и той же подвыборке)

## Ссылка на репозиторий со Stable Diffusion, который мы использовали для генерации изображений: 
https://github.com/AUTOMATIC1111/stable-diffusion-webui

## Датасет из сгенерированных изображений
Сгенерировано: 1416 изображений 
Аугментации: 90% rotate, Crop 0-40%, Grayscale 15%, Noise 1.05% of pixels 
Train set: 3648 
Test set: 200 
https://universe.roboflow.com/urfu-uavlq/stone_gen_new_segmentation/dataset/3


## Тесты. Пример работы детекции

| |Модель 1|Модель 2|Модель 3|
| ---|---|---|---|
| Instances|141|181|170|

Количество определённых моделями экземпляров(Instances)

## Итоговые результаты

| Dataset|bbox|bbox|segm|segm|max_gets|
| ---|---|---|---|---|---|
| Dataset|Average Precision (IoU=0.50:0.95)|Average Recall (IoU=0.50:0.95)|Average Precision (IoU=0.50:0.95)|Average Recall (IoU=0.50:0.95)|max_gets|
| KFold_0|0.599|0.691|0.642|0.706|72|
| KFold_1|0.625|0.710|0.664|0.728|86|
| KFold_2|0.598|0.692|0.631|0.700|92|
| KFold_3|0.592|0.684|0.649|0.712|94|
| KFold_4|0.574|0.670|0.638|0.703|91|
| Full|0.572|0.672|0.603|0.668|98|
| KFold_0_ru|0.557|0.643|0.585|0.644|128|
| KFold_1_ru|0.556|0.635|0.572|0.629|126|
| KFold_2_ru|0.540|0.633|0.559|0.630|116|
| KFold_3_ru|0.599|0.681|0.614|0.675|130|
| KFold_4_ru|0.566|0.658|0.596|0.659|125|
| Stone_ru|0.503|0.601|0.520|0.594|106|

Измерения точности проводились на ТЕСТОВОЙ ЧАСТИ, полученной от РЕАЛЬНОГО ДАТАСЕТА(Stone_ru)

## Сравнение KFOLD и KFOLD_RU. Анализ результатов

| Набор данных|bbox Average Precision|bbox Average Precision|segm Average Precision|segm Average Precision|
| ---|---|---|---|---|
| Kfold|mean|std|mean|std|
| Kfold|0,597600|0,016378|0,644800|0,011232|
| Kfold|bbox Average Recall|bbox Average Recall|segm Average Recall|segm Average Recall|
| Kfold|mean|std|mean|std|
| Kfold|0,689400|0,012955|0,709800|0,009927|
| Kfold_ru|bbox Average Precision|bbox Average Precision|segm Average Precision|segm Average Precision|
| Kfold_ru|mean|std|mean|std|
| Kfold_ru|0,56359|0,01958|0,58520|0,01900|
| Kfold_ru|bbox Average Recall|bbox Average Recall|segm Average Recall|segm Average Recall|
| Kfold_ru|mean|std|mean|std|
| Kfold_ru|0,65000|0,01782|0,64740|0,01760|

## Ссылки на результаты. Выводы

Модели, обученные на сгенерированных данных, демонстрируют высокую точность и стабильность, особенно при решении задач сегментации. Это подтверждает их способность качественно работать с синтетическими изображениями. 

Однако, их производительность ухудшается при дообучении на реальных данных, что может быть связано с несоответствием распределений или структурных особенностей синтетических и реальных данных. 

Модели, обученные исключительно на реальных данных, показывают ограниченные результаты из-за небольшого объёма данных. 

![QR со ссылкой на датасет](https://github.com/AAEfimov/Rock_fragmentation_benchmark/blob/main/qrcode%20-%20universe.roboflow.com_.png)

## Планы по дальнейшему развитию проекта

Дальнейшие перспективы:

1. Необходимо увеличить объём реальных данных для улучшения обучения. 
2. Можно применить дополнительные методы доменной адаптации (например, transfer learning или fine-tuning) для более успешной интеграции синтетических и реальных данных. 
3. Желательно исследовать более сложные аугментации и синтетические данные, чтобы улучшить генерацию данных, приближая их к реальным.


