project_path: /web/_project.yaml
book_path: /web/resources/_book.yaml
description: Если у вас короткое видео и вы хотите, чтобы оно воспроизводилось автоматически - в основном, если это GIF - то это руководство, возможно, то, что вам нужно.

{# wf_updated_on: 2017-04-26 #}
{# wf_published_on: 2016-10-12 #}

# Добавление видео {: .page-title }

{% include "web/_shared/contributors/surma.html" %}

Если вы хотите добавить видео в ваше статью, рассмотрите возможность добавления
его на [**YouTube**](https://youtube.com). YouTube автоматически адаптируется к
ширине канала пользователя и предлагает знакомый интерфейс. Однако, если у вас
короткое видео и вы хотите, чтобы оно воспроизводилось автоматически - в
основном, если это GIF - то это руководство, возможно, то, что вам нужно.

## Видео

Ваше видео должно быть **высокого качества** и иметь **высокую кадровую
частоту**. Если вы записываете скринкаст, убедитесь, что вы выставили достаточно
высокий битрейт, чтобы избежать артефактов в исходном материале. (Для ретина
экранов я обычно подходит ~6Mbit/s). Я рекомендую всегда записывать **не менее
60 fps**, *особенно*
когда записваете эффекты, переходы или любую анимацию.

## Преобразование в веб-форматы

Я использую [ffmpeg](https://www.ffmpeg.org/) для конвертации видео. Если у вас
Mac с установленным [Homebrew](http://brew.sh/), вы можете установить ffmpeg
так:

```
brew install --with-{libvpx,x265,openh264} ffmpeg
```

Чтобы убедиться, что видео проигрываются и в мобильном Safari и в других
браузерах, мы создадим две версии видео. Одна версия будет упакована в MPEG4
контейнер (`.mp4`) используя кодек h264. Другая версия будет упакована в WebM
контейнер (`.webm`) используя кодек VP8. Вы можете использовать следующий скрипт
для автоматизации процесса:

```
#!/bin/bash
BITRATE=${BITRATE:-500k}

ffmpeg -i $1 ${@:2} -c:v libx264 -profile:v main -level 4.0 -preset veryslow -tune animation -movflags +faststart -b:v ${BITRATE} -pix_fmt yuv420p -c:a null ${1%.*}_x264.mp4
ffmpeg -i $1 ${@:2} -c:v libvpx -b:v ${BITRATE} -c:a null ${1%.*}_vp8.webm
```

Обратите внимание:

- Используется: `BITRATE=500k ./transcode_video.sh <filename> [дополнительные
ffmpeg параметры]`
- Скрипт *удалит* любое аудио.
- Вы должны изменить параметр `-tune` на `film` если это не запись с экрана.
- Описание параметров кодировщика x264 можете найти
[здесь](https://trac.ffmpeg.org/wiki/Encode/H.264)
- Описание параметров кодировщика VP8 можете найти
[здесь](https://trac.ffmpeg.org/wiki/Encode/VP8)
- Возможно, вам захочется поиграть с битрейтом для каждого формата в
отдельности.VP8 имеет тенденцию к достижению такого же качества с меньшим
битрейтом (и, следовательно, меньшим размером файла), чем h264.

## Полезные опции

- `-r 15`: Сокращает до 15 fps. Я использую это когда я записываю с экрана
DevTool консоль или терминал.
- `-vf 'scale=trunc(iw/4)*2:-2'`: Делает видео вдвое больше. Когда используете
это, убедитесь, что и ширина и высота кратны двум (Это требование для h264).
- `-vf 'scale=trunc(iw/8)*4:-2'`: Делает то же, что и предыдущий пункт, за
исключением того, что делает видео вчетверо больше, а не вдвое.

Я бы порекомендовал не использовать исходное разрешение для видео, так как я
думаю, что никто не будет открывать видео на десктопе на полный экран. Ширина
чуть менее 1000px отличный выбор.

## Хостинг

Видео не хранятся в репозитории. Любой сторонний хостинг должен быть хорошим
(как GCS или S3). Гуглеры могут получить доступ к GCS для WebFundamentals
материалов.

## Добавление

Добавляйте видео в вашу статью, используя следующую разметку. Атрибут `autoplay`
может быть удалён, если автозапуск не нужен. Я рекомендую оставить атрибут
`controls`, поскольку iOS 9 ингорирует `autoplay`  и пользователю нужна
возможность запустить видео. Я также рекомендую [добавить
постер](/web/fundamentals/media/video#include_a_poster_image),
особенно, когда автозапуск недоступен.

```
<video controls autoplay loop muted poster="[url to poster image]">
  <source src="[url to .webm file]" type="video/webm; codecs=vp8">
  <source src="[url to .mp4 file]" type="video/mp4; codecs=h264">
</video>

```



Translated by
{% include "web/_shared/contributors/dmitryskripunov.html" %}