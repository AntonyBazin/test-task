## Test task
Необходимо выполнить:

1) Подготовить реляционную СУБД с таблицей requests со следующими полями:
    - id (serial, autoincrement)
    - request_uuid (varchar(36))
    - request_date (datetime)
    - attachment (json)

2) Разработать HTTP-Api с одним эндпоинтом, отдающее в ответ json-документ следующего вида:
```
{
  "request_uuid": "",
  "request_date": "",
  "attachment": {
    "entity": {
      "entity": {
        "entity": {
          ...
        }
      }
    }
  }
}
```
Значения полей, отдаваемых в ответе на HTTP-запрос должны иметь следующий вид:
 - request_uuid - уникальный идентификатор запроса (uuidv4), должен генерироваться автоматически для каждого входящего HTTP-запроса
 - request_date - дата и время прихода запроса в UTC (в формате isoformat)
 - attachment - вложенный json-объект, уровень вложенности определяется параметром attachment_depth, передающимся в query parameters HTTP-запроса

Результат обработки каждого HTTP-запроса должен записываться в подготовленную ранее СУБД в соответствующие поля.
Сервис с HTTP-Api необходимо упаковать в Docker-образ (наличие Dockerfile обязательно).

3) Разработать Python-скрипт, который посылает N запросов в разработанное Api и на каждый запрос получает результат в виде json. Далее по полученному request_uuid получает из подготовленной ранее СУБД нужную запись и выводит на экран соответствие request_uuid и id (значение, полученное из СУБД).
Скрипт должен выводить на экран прогресс выполнения запросов (сколько выполнено из скольки).
Параметры N (количество запросов в Api) и attachment_depth (глубина вложенного json, с которым будет отвечать Api) должны передаваться через аргументы командной строки.
Скрипт должен стабильно работать при N = 5000.

Общие требования:
 - СУБД - любая реляционнная
 - фреймворк для api - aiohttp
 - версия Python >= 3.8
 - для развертывания Api и СУБД необходимо использовать docker-compose

Приветствуется "распараллеливание" кода в скрипте, который отправляет запросы.

---

Приложение и скрипт для отправки запросов упакованы в 2 образа: базы данных и
непосредственно приложения.

### Файлы
* `runner.py` - содержит точку входа в приложение (п.2), используется для 
запуска сервиса;
* `app/moсk.py`  - генерирует 3 записи фиктивных данных для БД,
**удаляет** имевшиеся данные;
* `init_db.py`  - реализует подключение к БД, создание таблицы;
* `tester.py` - скрипт, посылающий N запросов к серверу. 
Реализует пункт 3 задания. принимает следующие аргументы при запуске
из командной строки:
> `-n`, `--N=` - число запросов;
> 
> `-d`, `--depth=` - глубина запросов;
> 
> `-h`, `--help` - отобразить подсказку;
> 
> `-v`, `--verbose` - отвечает за отображение данных, считываемых из БД.
> Если произвести запуск скрипта с этим аргументом, данные будут 
> выводиться непосредственно в консоль (что может быть неудобно). 
> Если запускать **без** этого аргумента, результаты сопоставления
> значений будут записываться в файл `log.txt`, который будет создан 
> при работе программы.
