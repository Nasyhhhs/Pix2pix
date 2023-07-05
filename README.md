# Pix2Pix + SuperResolution Telegram Bot 


Данный проект выполен в качестве итогового проекта обучения на курсе Deep Learning School. 
- Выполнение его включало три этапа:
- Генерация собственного датасета
- Выбор архитектуры модели и ее обучение
- Упаковка телеграм бота и настройка функционала для пользователя
- Деплой бота на сервер



## Сборка датасета

Описание процесса сборки и подготовки датасета для использования в проекте.

## Обучение модели Pix2Pix

За основу была взята модель Pix2Pix из [оригинальной статьи](https://arxiv.org/abs/1611.07004v3)
Pix2Pix - это архитектура глубокого обучения, которая используется для задачи условной генерации изображений. Она позволяет преобразовывать изображения из одного домена в изображения другого домена с помощью сопоставления пар изображений вход-выход.

Архитектура Pix2Pix основана на генеративно-состязательных сетях (GAN) и состоит из двух основных компонентов: генератора и дискриминатора. Генератор преобразует входное изображение в выходное изображение, а дискриминатор оценивает, насколько хорошо генератор выполнил свою задачу. Обучение модели происходит чередованием обновления генератора и дискриминатора, чтобы достичь более качественного преобразования изображений.

Pix2Pix широко используется в различных задачах, включая перевод стиля изображений, синтез текстур, цветизацию черно-белых изображений, создание фотореалистичных изображений на основе набросков и другие. Она также находит применение в области компьютерного зрения, где может быть использована для сегментации объектов, восстановления изображений или улучшения разрешения изображений.

Как и другие модели GAN, Pix2Pix состоит из двух моделей : 
Генератора и Дискриминатора
Если последний мало чем отличается от других GAN, на генераторе остановимся подробнее.
Его структура основана на модели UNetоснован на модифицированной версии UNet. UNet - это сверточная нейронная сеть, изначально разработанная для задач сегментации изображений. Архитектура UNet получила широкое признание благодаря своей способности сохранять детали и выполнять точные сопоставления между входными и выходными изображениями.

Генератор в Pix2Pix состоит из энкодера, декодера и пропускных соединений (skip connections), что является ключевым аспектом UNet. Энкодер принимает входное изображение и постепенно уменьшает его размерность, извлекая информацию о содержимом изображения на разных уровнях абстракции. Затем, декодер последовательно увеличивает размерность и генерирует выходное изображение на основе этих абстрактных представлений. Пропускные соединения позволяют передавать информацию между энкодером и декодером на разных уровнях, что помогает сохранить детали и структуру изображения.

Помимо стандартной архитектуры UNet, генератор Pix2Pix также включает в себя операции свертки, обратной свертки и нормализации. Оптимизация генератора происходит путем минимизации разницы между сгенерированными изображениями и целевыми изображениями с использованием функции потерь, например, L1 или L2-нормы.

Таким образом, генератор на основе UNet в архитектуре Pix2Pix способен преобразовывать входные изображения в соответствующие выходные изображения с сохранением деталей и структуры, что отлично подходит для переноса цветовой схемы.
## Super-Resolution


https://github.com/eugenesiow/super-image

## Telegram-bot

Описание Telegram-бота

## Деплой на сервер

Инструкции по развертыванию проекта на сервере.

## Демонстрация

Примеры и описание демонстрации работы





