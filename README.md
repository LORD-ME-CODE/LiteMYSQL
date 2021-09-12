# LiteMYSQL 
База данных? Без проблем!

# КРАТКАЯ ДОКУМЕНТАЦИЯ
Привет! Эта библиотека создана для простого и быстрого создания и редактирования базы данных формата MYSQL.
 Библиотека основана на pymysql, aiomysql

Pypi - https://pypi.org/project/Lite-MYSQL

## Импорты
Рекомендуем использовать from Lite_MYSQL import lmysql или же from Lite_MYSQL import aiolmysql (они будут рассмотрены тут), но есть и другие варианты импорта.

## _=lmysql(host='localhost', user='root', password='', db='', charset='utf8-general-ci', port=3306, log=True)
Соединяется с базой данных

host = хост, на котором расположен сервер базы данных, по умолчанию: localhost
user = имя пользователя для входа в систему, по умолчанию: root
password = пароль для использования
db = Название базы данных
port = Порт, по умолчанию 3306
log = Логгирование, по умолчанию True


## _=aiolmysql(host='localhost', user='root', password='', db='', charset='utf8-general-ci', port=3306, log=True)
Соединяется с базой данных (async)

host = хост, на котором расположен сервер базы данных, по умолчанию: localhost
user = имя пользователя для входа в систему, по умолчанию: root
password = пароль для использования
db = Название базы данных
port = Порт, по умолчанию 3306
log = Логгирование, по умолчанию True

## _.create(names, table="albums")
Создает столбцы таблицы

Название  | Что это?
------------- | -------------
names | строка в виде названий столбцов через ", ", например "id(INT), name(TEXT)"
table | название подтаблицы, по умолчанию albums (не спрашивайте почему) - больше не буду про него писать

## _.insert_data(data_mass, table_rows, table="albums")
Вставляет данные в таблицу по строкам с помощью массива

Название  | Что это?
------------- | -------------
data_mass | Массив с кортежами через запятую (если их несколько), например [("Вася", "Покровская")] для одного заполнения одной строки из 2 столбцов или [("Петя", "Ленина"), ("Лена", "Уфимская")] для заполнения сразу 2-х строчек из 2-х столбцов.
table_rows | Строка с названиями столбцов таблицы, например "id, name, balance".

## _.edit_data(title_last, last, title_new, new, table="albums")
Изменяет данные в конкретной ячейке таблицы
Название  | Что это?
------------- | -------------
title_last | название столбца поиска 
last | данные, которые мы ищем в конкретном столбце для выявления строки (строк)
(предыдущие 2 | всего лишь находят строку, в которой надо что-то изменить, но никак не влияют на изменение)
title_new | название столбца, данные в котором надо изменить 
new | данные, которые нужно вписать в ячейку конкретного столбца этой строки

## _.delete_data(name, title_name, table="albums")
Удаление строки (строк), где присутсвует конкретное имя в конкретном столбце

Название  | Что это?
------------- | -------------
name | данные, которые мы ищем в конкретном столбце для последующего удаления найденой строки
title_name | столбец для поиска данных

## _.select_data(name, title, row_factor=False, table="albums")
Поиск и возврат данных в переменную по данным и столбцу

Название  | Что это?
------------- | -------------
name | данные, которые мы ищем в конкретном столбце для вывода строки
title | название столбца поиска
row_factor | тип вывода данных, по умолчанию выключен и выводится в формате fetchone, если передать True - вернется в fetchall

## _.select_data_with_sort(type_sort, name, title_name, table="albums")
Тоже самое, что и с _.select_data, но с сортировкой. name можно придать None, и тогда вернется только столбик

Название  | Что это?
------------- | -------------
type_sort | тип сортировки (подробнее в документации mysql)
name | данные, которые мы ищем в конкретном столбце для вывода строки
title | название столбца поиска

## _.search(type_search, name_search, table="albums")
Поиск данных в БД и возврат их в переменную

Название  | Что это?
------------- | -------------
type_search | тип поиска (например - title - столбцы)
name_search | имя, которое мы ищем (в том числе с помощью неопределенности - например % может искать любое количество любых символов в имени, даже 0), например "у%ица"

## _.get_all_data(table="albums")
Возвращает всю таблицу.

## _.get_cursor()
Вернет вам курсор для ваших запросов)

## _.get_connect()
Вернет вам коннект с бд для ваших запросов)

# Примеры
```python
from LMSQL import lmysql
sql = lmysql() #Соединяемся с БД 
sql.create('id, hash') #Создаем 2 столбца - id и hash
sql.insert_data((1, 'lord'), 'id, name') #Добавляем данные
a = sql.select_data(1, 'id') #Ищем строку, в которой id = '234'
print(a) #Результат - [{'id':1, name:'lord'}]
sql.edit_data('id', 1, 'name', 'genius') #Изменяем данные - там, где id = 234, теперь hash = 1234
a = sql.select_data(1, 'id') #Ищем строку, в которой id = 1
print(a) #Результат - [{'id':1, name:'genius'}]
b = sql.search('name', 'dima123') #Поиск строк 
print(b) #Результат - [{'id':2, 'name':'dima123')]
a = sql.select_data_with_sort('rowid', None, 'id') #Сортировка строк по возрастанию данных в id
print(a) #Результат - [{'id':1, 'name':'lord'}, {'id':2, 'name':'dima'}]
a = sql.get_all_data() #Вернем всю таблицу?
print(a) #Результат - [{'id':1, 'name':'lord'}, {'id':2, 'name':'dima'}]
```

```python
from LMSQL import aiolmysql
import asyncio

async def main():
    sql = aiolmysql() #Соединяемся с БД
    await sql.connect() #Соединяемся с БД
    await sql.create('id, hash') #Создаем 2 столбца - id и hash
    await sql.insert_data((1, 'lord'), 'id, name') #Добавляем данные
    a = await sql.select_data(1, 'id') #Ищем строку, в которой id = '234'
    print(a) #Результат - [{'id':1, name:'lord'}]
    await sql.edit_data('id', 1, 'name', 'genius') #Изменяем данные - там, где id = 234, теперь hash = 1234
    a = await sql.select_data(1, 'id') #Ищем строку, в которой id = 1
    print(a) #Результат - [{'id':1, name:'genius'}]
    b = await sql.search('name', 'dima123') #Поиск строк 
    print(b) #Результат - [{'id':2, 'name':'dima123')]
    a = await sql.select_data_with_sort('rowid', None, 'id') #Сортировка строк по возрастанию данных в id
    print(a) #Результат - [{'id':1, 'name':'lord'}, {'id':2, 'name':'dima'}]
    a = await sql.get_all_data() #Вернем всю таблицу?
    print(a) #Результат - [{'id':1, 'name':'lord'}, {'id':2, 'name':'dima'}]

asyncio.run(main())
```

# Контакты

Что-то не работает, есть вопросы, пожелания? Пиши - t.me/lord_codes


# Удачи!
