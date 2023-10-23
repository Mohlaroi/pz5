На SIGGRAPH 2023 появилась статья "3D-Разбрызгивание по Гауссу для рендеринга поля сияния в реальном времени" Керб, Копанаса, Леймкюлера, Дреттакиса, которая выглядит довольно круто! Ознакомьтесь с их веб-сайтом, репозиторием исходного кода, наборами данных и так далее.

Я решил попробовать реализовать часть визуализации в реальном времени (то есть ту, которая использует уже созданный файл "модели" gaussian splat) в Unity.

Скриншот

Исходный бумажный код имеет исключительно средство визуализации в реальном времени на основе CUDA; другие люди сделали свои собственные реализации (например, WebGPU в cvlab-epfl, Taichi в wanmeihuali и т.д.).

Код, приведенный здесь до сих пор, случайным образом собран из прочтения статьи (а также более ранней литературы по разбрызгиванию EWA), просмотра официальной реализации CUDA и так далее. Текущее состояние:

В коде не используется фрагмент из статьи "растризатор разбрызгивания на основе плиток"; он просто рисует каждый разбрызгивание по гауссу в виде ориентированного квадрата, который охватывает его экстенты.
Накопление цвета разбрызгивания выполняется путем рендеринга спереди назад с режимом наложения, в результате которого получается тот же накопленный цвет, что и в их рендере на основе плиток.
Сортировка по разбиению выполняется с помощью сортировки по основанию, производной от AMD FidelityFX.
Использование
⚠️ Примечание: это все игрушка, с которой я просто играюсь. Если вы подадите сообщения об ошибках или запросах функций, я, скорее всего, просто проигнорирую их и буду делать все, что захочу. Я же тебе говорил! ⚠️

Загрузите или клонируйте этот репозиторий и откройте projects/GaussianExample как проект Unity (я использую Unity 2022.3, могут работать и другие версии). Обратите внимание, что для проекта требуется DX12 или Vulkan в Windows, т.е. DX11 работать не будет. Это вообще не тестировалось на мобильных устройствах / в Интернете и, вероятно, там не работает.



Затем создайте некоторые ресурсы GaussianSplat: откройте Tools -> Gaussian Splats -> Create GaussianSplatAsset меню в Unity. В диалоговом окне укажите Input PLY File на ваш файл разбрызгивания по Гауссу (обратите внимание, что это должен быть файл разбрызгивания по гауссу, а не какой-либо другой файл разбрызгивания. Например, в официальных бумажных моделях правильные файлы указаны под point_cloud/iteration_*/point_cloud.ply). По желанию это может быть cameras.json рядом с ним или где-нибудь в родительских папках.

Выберите нужные параметры сжатия и выходную папку и нажмите кнопку "Создать ресурс". Сжатие даже при "очень низком" качестве вполне приемлемо, например, общий размер этого снимка при очень низкой настройке составляет менее 8 МБ (нажмите, чтобы посмотреть видео):
Смотрите видео

Если бы все было в порядке, должен быть ресурс GaussianSplat, рядом с которым есть несколько файлов данных.

Поскольку модели разбрызгивания по гауссу довольно большие, я не включил ни одну из них в это репозиторий Github. На оригинальной бумажной странице github есть ссылка на 14-гигабайтный zip-файл их моделей.

В игровом объекте, у которого есть GaussianSplatRenderer скрипт, укажите поле Asset на один из созданных вами ресурсов. В скрипте есть различные элементы управления для отладки / визуализации данных, а также ползунок для перемещения игровой камеры в одно из местоположений asset.

При рендеринге учитывается матрица преобразования игрового объекта; официальные модели разбрызгивания по гауссу, похоже, все повернуты примерно на -160 градусов вокруг оси X и зеркально отражены вокруг оси Z, поэтому в образцовой сцене для объекта настроено такое преобразование.

Дополнительная документация:

Интеграция конвейера рендеринга
Редактирование разбрызгиваний
Вот и все!

Планов на будущее, дорожной карты и списка задач на самом деле не существует, я просто буду делать то, что я случайно решу. Я сохраняю некоторые открытые идеи в виде выпусков github.