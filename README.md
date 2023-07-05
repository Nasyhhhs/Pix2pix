# Pix2Pix + SuperResolution Telegram Bot 


Данный проект выполнен в качестве итогового проекта обучения на курсе “Deep Learning -Продвинутый поток” от МФТИ. 
Выполнение его включало три этапа:
- Генерация собственного датасета
- Выбор архитектуры модели и ее обучение
- Упаковка телеграм бота и настройка функционала для пользователя
- Деплой бота на сервер

## Демонстрация




## Сборка датасета
Моя идея состояла в том, чтобы экспериментировать с нестандартным подходом к раскраске изображений, а не просто переводить черно-белые снимки в цветные. Я хотела, чтобы обычные фотографии заиграли сочными неоновыми переливами как в киберпанк-играх. Именно они послужили источником вдохновения для моей цветовой гаммы.
Для создания датасета я самостоятельно собрала около 3000 изображений размером 256x256 путем парсинга сочных неоновых скриншотов с сайта flickr. 
![ds](https://github.com/Nasyhhhs/Pix2Pix-superRes-bot/assets/109277211/f69c4e76-64b3-4de5-bed4-1a3e12cd560d)

Код, который я использовала находится в папке dataset. 

## Архитектура Pix2Pix
![2023-07-05_150708](https://github.com/Nasyhhhs/Pix2Pix-superRes-bot/assets/109277211/c7b0f2ac-c2d5-429f-b8a3-b07f81746127)

 
За основу была взята модель Pix2Pix из [оригинальной статьи](https://arxiv.org/abs/1611.07004v3)

Pix2Pix - это архитектура глубокого обучения, которая используется для задачи условной генерации изображений. Она позволяет преобразовывать изображения из одного домена в изображения другого домена с помощью сопоставления пар изображений вход-выход, иными словами, генерировать новые изображения, обучившись на парах входных и выходных изображений.
В моем случае я использовала пары изображений из вышеописанного датасета, сформировав два даталоадера – цветных и черно-белых изображений, убрав цветовую информацию из основного датасета.
Pix2Pix широко используется в различных задачах, включая перевод стиля изображений, синтез текстур, цветизацию черно-белых изображений, создание фотореалистичных изображений на основе набросков и другие. Она также находит применение в области компьютерного зрения, где может быть использована для сегментации объектов, восстановления изображений или улучшения разрешения изображений.

Архитектура Pix2Pix основана на генеративно-состязательных сетях (GAN) и состоит из двух моделей : 
Генератора и Дискриминатора
Генератор преобразует входное изображение в выходное изображение, а дискриминатор оценивает, насколько хорошо генератор выполнил свою задачу. Обучение модели происходит чередованием обновления генератора и дискриминатора, чтобы достичь более качественного преобразования изображений.

Структура генератора основана на модифицированной версии UNet. 

UNet - это сверточная нейронная сеть, изначально разработанная для задач сегментации изображений. 
![c8b56ae4-47ef-42d7-b651-9745c90911ba](https://github.com/Nasyhhhs/Pix2Pix-superRes-bot/assets/109277211/29146a6d-9a3d-4ca9-abe4-d3079135accf)


Генератор в Pix2Pix состоит из энкодера, декодера и пропускных соединений (skip connections), что является ключевым аспектом UNet. Энкодер принимает входное изображение и постепенно уменьшает его размерность, извлекая информацию о содержимом изображения на разных уровнях абстракции. Затем, декодер последовательно увеличивает размерность и генерирует выходное изображение на основе этих абстрактных представлений. Пропускные соединения позволяют передавать информацию между энкодером и декодером на разных уровнях, что помогает сохранить детали и структуру изображения.
![Без названия](https://github.com/Nasyhhhs/Pix2Pix-superRes-bot/assets/109277211/d827be31-3030-4dae-b468-01a50177b798)

 
Помимо стандартной архитектуры UNet, генератор Pix2Pix также включает в себя операции свертки, обратной свертки и нормализации. 

Оптимизация генератора происходит путем минимизации разницы между сгенерированными изображениями и целевыми изображениями с использованием функции потерь (BCEWithLogitsLoss() и L1Loss()).
Таким образом, генератор на основе UNet в архитектуре Pix2Pix способен преобразовывать входные изображения в соответствующие выходные изображения с сохранением деталей и структуры, что отлично подходит для переноса цветовой схемы.


![2023-07-05_165337](https://github.com/Nasyhhhs/Pix2Pix-superRes-bot/assets/109277211/007340f5-0d92-4803-ac1d-66038e9c90eb)

![2023-07-05_165219](https://github.com/Nasyhhhs/Pix2Pix-superRes-bot/assets/109277211/b522e942-d751-45b8-b12e-8b9c9032afcb)

![2023-07-05_165316](https://github.com/Nasyhhhs/Pix2Pix-superRes-bot/assets/109277211/4db07c82-6709-45bd-9a51-5e013ccd10c5)

Результаты, полученные на разных эпохах:

![Без названия (3)](https://github.com/Nasyhhhs/Pix2Pix-superRes-bot/assets/109277211/bbeb46f8-2e98-47ff-baaf-b2a26268542d)
![epochs](https://github.com/Nasyhhhs/Pix2Pix-superRes-bot/assets/109277211/fb6ad192-539c-4ac5-b422-28e036754a15)
![learning_curve](https://github.com/Nasyhhhs/Pix2Pix-superRes-bot/assets/109277211/0e33b293-2f49-48d6-baef-56b7d5c9b4bd)

Обучение модели длилось 54 эпохи. Полный код процесса обучения модели находится в корневой папке репозитория в файле .ipynb.

## Super-Resolution
Поскольку обучение производилось на довольно небольших изображениях я решила подключить данную модель к своему проекту, чтобы изображения не сильно теряли в размере и качестве и вывод картинок более привлекательным. Так как это не основная модель проекта, я решила не уделять много времени ее обучению с нуля и использовала предобученную модель из библиотеки super-image: [Источник]https://github.com/eugenesiow/super-image

## Telegram-bot

Логику работы бота хотелось сделать интуитивной и немного игровой, чтобы она соответствовала стилю проекта:
- Пользователь заходит и видит кнопку START, нажимает и видит приветствие. 
- Есть кнопка меню в левом нижнем углу, где прописаны основные команды.
- После отправки картинки пользователю предлагаются два варианта – Neon (собственно применение цветовой схемы) и Upscaling, то есть увеличение размера изображения. Таким образом он может не только красиво раскрасить картинку, но и бонусом немного поднять ее качество ::)
- Размер картинки остается исходным после преобразования(под капотом еще апскейлинг и ресайз).
- Функции бота прописаны асинхронно, чтобы несколько пользователей могли пользоваться ботом параллельно.
- Файлы находятся в папке bot в текущем репозитории. Файл исполнения main.py

## Деплой на сервер


## Улучшение и развитие бота
Данный бот не является конечным и будет дорабатываться по мере моих сил и энтузиазма. Возможно в скором времени будет обучен на большем количестве эпох и датасете лучшего качества. Также возможно будут добавлены новые функции и возможности работы с изображениями. 

## Примеры
![2](https://github.com/Nasyhhhs/Pix2Pix-superRes-bot/assets/109277211/ad8eadc3-7547-42a3-a9ef-729620b6569d)
![2_up](https://github.com/Nasyhhhs/Pix2Pix-superRes-bot/assets/109277211/75ed52b3-d0c9-410b-9aa3-5fb046f64ead)
![Без названия](https://github.com/Nasyhhhs/Pix2Pix-superRes-bot/assets/109277211/e2e8560d-9ab8-42ec-a33f-b055fec1f088)
![photo_5274098373974149997_m](https://github.com/Nasyhhhs/Pix2Pix-superRes-bot/assets/109277211/2f8cfdf8-f6fe-4c16-83dd-c44e410222a0)

Больше примеров можно найти в папке examples текущего репозитория ::)






