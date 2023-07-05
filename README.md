# Pix2Pix + SuperResolution Telegram Bot 


Данный проект выполнен в качестве итогового проекта обучения на курсе “Deep Learning -Продвинутый поток” от МФТИ. 
- Выполнение его включало три этапа:
- Генерация собственного датасета
- Выбор архитектуры модели и ее обучение
- Упаковка телеграм бота и настройка функционала для пользователя
- Деплой бота на сервер

## Сборка датасета
Моя идея состояла в том, чтобы экспериментировать с нестандартным подходом к раскраске изображений, а не просто переводить черно-белые снимки в цветные. Я хотела, чтобы обычные фотографии заиграли сочными неоновыми переливами как в киберпанк-играх. Именно они послужили источником вдохновения для моей цветовой гаммы.
Для создания датасета я самостоятельно собрала около 3000 изображений размером 256x256 путем парсинга сочных неоновых скриншотов с сайта flickr. 
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

 
Помимо стандартной архитектуры UNet, генератор Pix2Pix также включает в себя операции свертки, обратной свертки и нормализации. Оптимизация генератора происходит путем минимизации разницы между сгенерированными изображениями и целевыми изображениями с использованием функции потерь, например, L1 или L2-нормы.

Таким образом, генератор на основе UNet в архитектуре Pix2Pix способен преобразовывать входные изображения в соответствующие выходные изображения с сохранением деталей и структуры, что отлично подходит для переноса цветовой схемы.
Полный код процесса обучения модели находится в корневой папке репозитория в файле .ipynb.

## Super-Resolution
Поскольку обучение производилось на довольно небольших изображениях я решила подключить данную модель к своему проекту, чтобы изображения не сильно теряли в размере и качестве и вывод картинок более привлекательным. Так как это не основная модель проекта, я решила не уделять много времени ее обучению с нуля и использовала предобученную модель из библиотеки super-image: [Источник]https://github.com/eugenesiow/super-image

## Telegram-bot

Логику работы бота хотелось сделать интуитивной и немного игровой, чтобы она соответствовала стилю проекта:
Пользователь заходит и видит кнопку START, нажимает и видит приветствие. 
Также присутствует кнопка меню в левом нижнем углу, где прописаны основные команды.
Наконец, поигравшись с UI, пользователь отправляет фото. При этом ему предлагаются два варианта – Neon (собственно применение цветовой схемы) и Upscaling, то есть увеличение размера изображения. Таким образом он может не только красиво раскрасить картинку, но и бонусом немного поднять ее качество.
Функции бота прописаны асинхронно, чтобы несколько пользователей могли пользоваться ботом параллельно.
Файлы находятся в папке bot в текущем репозитории. Файл исполнения main.py

## Деплой на сервер

## Демонстрация







