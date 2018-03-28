## Homework -5

### Задача:

Нужно спарсить файл с данными о городах и записать эти данные в Базу данных в Django.

Сам файлик с городами тут: https://www.maxmind.com/en/free-world-cities-database

По этой же ссылки можно найти, что значит каждое из слов отделенных запятой в строке:

    Country Code
    ASCII City Name
    City Name
    Region
    Population
    Latitude (The approximate latitude of the postal code, city, subdivision or country associated with the IP address.*)
    Longitude (The approximate latitude of the postal code, city, subdivision or country associated with the IP address.*)
    

#### После того как спарсите: 
нужно вывести эти города на страницу с городами использую ListView с пагинацией по 100 городов. В ListView отображаем только название города и ссылку на DetailView города , а в самой DetailView города должны отображаеться все его поля. 

### Подсказки:

#### Менеджмент команда
Вы уже знаете manage.py команды и часто их используете. 
Так вот для решения такой задачи Вам нужно будет создать свою manage.py команду и вызвать ее так же как вы вызывали другие команды из manage.py.

Почитать об этом можно тут: https://djbook.ru/rel1.9/howto/custom-management-commands.html

Пример кода такой команды:

```python
from django.core.management.base import BaseCommand
from apps.articles.models import Article


class Command(BaseCommand):
    help = 'Обнуляем дзен'

    def handle(self, *args, **options):
        Article.objects.filter(on_dzen=True).update(on_dzen=False)
        self.stdout = 'Дзен обнулен'
```

В этом def handle вам как раз и нужно написать код парсера файла.

#### Парсер:

Раз вы должны спарсить файл, вам конечно же нужно открыть файл и пройтись по его строкам. Вы это уже делали на курсе по питону:
```python
  FilePath = "/home/user/Documents/file.txt"
  with open(FilePath, 'r') as file:
    for line in file:
      # Ваш код парсера
```
Строку как разбить по запятой вы должны знать. И так вы получите массив с данными для полей моделей. Останется только их записать в Модели.

Сам файлик с городами не храните в репозитории. Пусть он лежит где то в Documents как в примере кода.

Еще в идеале, можете прочитать докуменатацию узнать как принимать аргумент в Менеджмент команду и передайте путь к файлику через аргументы. Но это не обязательно.

#### Модели:

Чтоб сохранить эти данные в БД конечно же нужно создать модель для Городов с такими же полями как и в файле кроме поля Country Code. Для этого поля создайте отдельную модель. Модель Городов должно также иметь ForignKey на Country Code модель.

Пример того как нужно будет сохранять:

```python
сity = City(*передаем данные всем полям кроме country code*)
country_code = CountryCode.objects.get_or_create(code=code)  # get_or_create срабатывает как get() если такой объект есть и как create() если такого объекта нет.
city.country_code = country_code
city.save()
```

### Заключение:

Задача может показаться сложной, но если делать ее поэтапно , то все получится. Например можете сперва написать просто питон код вне джанги и попробовать спарсить файлик. Потом этот код переносите Менеджмент команду и запускаете ее принтуя результат работы. А потом уже последним шагом добавляете сохранение в модель вместо вывода через print.
