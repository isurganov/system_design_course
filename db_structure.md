Table Users {
  id                serial          [pk, increment]
  username          varchar(50)     [not null, unique]
  email             varchar(100)    [not null, unique]
  password_hash     varchar(255)    [not null]
  profile_picture   varchar(255)
  bio               text
  created_at        timestamp       [default: `now()`]
  updated_at        timestamp       [default: `now()`]
}

Table Posts {
  id                serial          [pk, increment]
  user_id           int             [ref: > Users.id]
  title             varchar(255)    [not null]
  description       text
  location          varchar(255)    [not null]
  created_at        timestamp       [default: `now()`]
  updated_at        timestamp       [default: `now()`]
}

Table PostImages {
  id                serial          [pk, increment]
  post_id           int             [ref: > Posts.id]
  image_url         varchar(255)    [not null]
  created_at        timestamp       [default: `now()`]
}

Table Comments {
  id                serial          [pk, increment]
  post_id           int             [ref: > Posts.id]
  user_id           int             [ref: > Users.id]
  content           text            [not null]
  created_at        timestamp       [default: `now()`]
}

Table Likes {
  id                serial          [pk, increment]
  post_id           int             [ref: > Posts.id]
  user_id           int             [ref: > Users.id]
  created_at        timestamp       [default: `now()`]
}

Table Follows {
  follower_id       int             [ref: > Users.id]
  followee_id       int             [ref: > Users.id]
  created_at        timestamp       [default: `now()`]
}

Table Messages {
  id                serial          [pk, increment]
  sender_id         int             [ref: > Users.id]
  receiver_id       int             [ref: > Users.id]
  content           text            [not null]
  created_at        timestamp       [default: `now()`]
}

Table PopularPlaces {
  id                serial          [pk, increment]
  country           varchar(100)    [not null]
  city              varchar(100)    [not null]
  place_name        varchar(255)    [not null]
  description       text
  created_at        timestamp       [default: `now()`]
}


Каждый пользователь публикует в среднем 1 пост в день.
Каждый пост содержит в среднем 3 фотографии.
Средний размер фотографии - 1 МБ.
Каждый пост имеет в среднем 10 комментариев.
Каждый пост получает в среднем 20 лайков.
Каждый пользователь подписан в среднем на 50 других пользователей.
Каждый пользователь отправляет в среднем 5 сообщений в день.

Вычисления:
Пользователи:

10,000,000 пользователей * 100 байт (основные данные) ≈ 1 ГБ
Посты:

10,000,000 постов * 200 байт (данные поста) ≈ 2 ГБ
Фотографии:

10,000,000 постов * 3 фото * 1 МБ ≈ 30 ТБ
Комментарии:

10,000,000 постов * 10 комментариев * 200 байт ≈ 20 ГБ
Лайки:

10,000,000 постов * 20 лайков * 100 байт ≈ 20 ГБ
Подписки:

10,000,000 пользователей * 50 подписок * 100 байт ≈ 50 ГБ
Сообщения:

10,000,000 пользователей * 5 сообщений * 200 байт ≈ 10 ГБ
Итог:
Пользователи: 1 ГБ
Посты: 2 ГБ
Фотографии: 30 ТБ
Комментарии: 20 ГБ
Лайки: 20 ГБ
Подписки: 50 ГБ
Сообщения: 10 ГБ

Общий объем данных:
30 ТБ + 1 ГБ + 2 ГБ + 20 ГБ + 20 ГБ + 50 ГБ + 10 ГБ ≈ 30.1 ТБ

Рекомендации по оборудованию:
Основное хранилище:

Использовать SSD для основной базы данных, чтобы обеспечить высокую скорость доступа и надежность.
Хранение фотографий на распределенной файловой системе или специализированном хранилище, таком как Amazon S3 или аналогичное.
Резервное копирование и репликация:

Регулярное резервное копирование данных.
Репликация базы данных для обеспечения отказоустойчивости.
Оценка нагрузки:

Для обработки 10,000,000 активных пользователей в день необходимо масштабируемое облачное решение с поддержкой горизонтального и вертикального масштабирования.

AWS io2:
Тип диска: Provisioned IOPS SSD (io2)

Требуемые диски:
При IOPS в 64,000 на диск потребуется: 100,000,000 IOPS / 64,000 IOPS ≈ 1563 дисков
При пропускной способности 1,000 MB/s на диск потребуется: (100,000,000 операций * 1 MB) / 1,000 MB/s ≈ 100,000 секунд ≈ 28 часов на 1 диск, следовательно, для равномерного распределения нагрузки также потребуется ≈ 1563 диска.

GCP SSD Persistent Disks:
Тип диска: SSD Persistent Disks

Требуемые диски:
При IOPS в 100,000 на диск потребуется: 100,000,000 IOPS / 100,000 IOPS = 1000 дисков
При пропускной способности 1,200 MB/s на диск потребуется: (100,000,000 операций * 1 MB) / 1,200 MB/s ≈ 83,333 секунд ≈ 23 часа на 1 диск, следовательно, для равномерного распределения нагрузки потребуется ≈ 1000 дисков.

Стоимость AWS io2:
Стоимость за диск: $0.125 за GB в месяц + $0.065 за предоставленные IOPS
При среднем размере диска 1 TB и 64,000 IOPS: $125 за диск + $4,160 за IOPS ≈ $4,285 в месяц на диск
Для 1563 дисков: 1563 * $4,285 ≈ $6,697,755 в месяц
GCP SSD Persistent Disks:
Стоимость за диск: $0.17 за GB в месяц
При среднем размере диска 1 TB: $170 за диск
Для 1000 дисков: 1000 * $170 ≈ $170,000 в месяц
Таким образом, для обеспечения надежной и масштабируемой системы хранения данных для вашей социальной сети путешественников можно выбрать либо AWS io2 диски для максимальной производительности, либо GCP SSD Persistent Disks для оптимизации затрат.