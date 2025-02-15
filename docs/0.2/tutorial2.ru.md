---
layout: default
title: tutorial2.ru
---

# tutorial2.ru

## Установка

Для нормальной работы программ с web.py необходимы сам web.py, flup и библиотеки баз данных.
Скачайте в один и тот же каталог следующие файлы:

* web.py (лучше из svn, командой **svn co http://webpy.org/svn/trunk/web**)
* flup - **wget http://www.saddi.com/software/flup/dist/flup-r2016.tar.gz**

после этого разожмите последний архив, скопируйте содержимое папки flup в каталог, где вы работаете, и удалите ненужный хлам. Например так:

    $ tar xf flup-r2016.tar.gz
    $ mv flup{-r2016/flup,}
    $ rm -r flup-r2016*

Таким образом мы подготовили среду для разработки.

## Поддержка URL

Самая важная часть любого файла это структура его URL. Эти адреса - не только буквы, которые ваши посетители видят и отправляют по почте друзьям, это ментальная модель работы вебсайта.
На популярных сайтах, таких как [del.icio.us](http://del.icio.us/), URL это вообще часть пользовательского интерфейса. web.py легко позволяет сделать клевые URLы.

Откройте в текстовом редакторе новый файл, назвав его ну... скажем... **poluekt.py**.
Шучу. Удобнее будет **code.py**. Впишите в него главную строку:

    import web

так импортируются функции фреймворка **web.py**.

    urls = (
       '/', 'index'    )

Это ваш лист соответствия urlов и функций. Первая  часть - регулярное выражение с помощью которого определяется путь. Например **'/'**, или **'/help/faq'**, или даже **/item/(\d+)**. Строка **d+** обозначает "некоторая ненулевая последовательность цифр", подробнее смотрите об этом в [Python Regex HowTo](http://www.amk.ca/python/howto/regex/) или воспользуйтесь [удобной генерилкой regex](http://osteele.com/tools/rework/). Скобки вокруг **\d+** нужны для того, чтобы удобно исользовать эти цифры в дальнейшем. Вторая часть - имя класса, которому будет передан запрос. Например, **'index'**, **'welcomes.hello'** (то есть класс **hello** из модуля **welcomes**) или **'get_\1'**. **\1** заменяется на первое совпадание выделенного регулярного выражения. Все остальные элементы выделенного регулярного выражения (помните **(\d+)**? ) передаются в класс-обработчик.

Собственно в нашем случае сказано что URL "/" (т.е. первая страница) будет обрабатываться классом **'index'**

Теперь нам надо написать сам класс. В то время как большая часть людей не обращая ни на что внимание бродит по сайтам, внутри веба используется архитектурный стиль, известный как REST. Детали не так уж и важны, но основная идея в том, что посетители сайтов просят сервер применить определенные действия (например "GET" и "POST") на ссылки (такие как "/" или "/wtf?f=1").

"GET" это то, к чему мы все привыкли, он используется чтобы получить содержимое страницы. Когда вы открываете сайт "yandex.ru" в своем браузере, на самом деле вы как бы просите Яндекс взять ("GET" от английского "to get" - брать) страницу '/', командой "GET /". "POST", вторая популярная функция, от английского to post - записать, внести в списки. Логично предположить что с помощью этой команды вы просите _что-либо_сделать_, например зачарджить кредитную карту и подтвердить заказ. В этом и есть фича, потому что "GET" может свободно использоваться везде, например индексироваться яндексом, что не слишком хорошо для страниц с заказами. Представьте себе что робот яндекса пришел на ваш сайт и заказал все товары!

## Разработка

В нашем коде для web.py мы сделаем так:

    class index:
       def GET(self):
            print "Превед, Орлы!!!"            # Таким образом постетителю страницы '/' будет показан русский вариант строки "Hello World!".
    if __name__ == "__main__": web.run(urls, globals())

Этот страшный набор букв говорит, что нужно запустить наше веб-приложение при исполнении файла.
Первый аргумент вызова **web.run**, urls, это тот самый список-соответствие url'ов и функций, описаный выше.

У web.py также есть средства для отладки, например добавьте перед последней строкой:
    web.webapi.internalerror = web.debugerror

Теперь при ошибке вы будете читать нормальный, человеко-читаемый бэктрейс. Если в последней строке вписать последним аргументом **web.reloader** будет еще лучше.
Это очень удобная опция, заставляющая web.py перезагружать исполняемый файл каждый раз, когда в нем что-то изменено. Постарайтесь не забыть убрать и этот параметр перед публичным запуском вашей страницы. Кстати, если вместо **web.reloader** вписать **web.profiler** можно будет узнавать сколько времени занимает та или иная функция при выдаче страницы. Говоря человеческим языком, это *профайлер*, то есть средство, помогающее оптимизировать скорость работы скрипта.

Настало время запускать наше приложение. Просто выполните команду

    $ python code.py

и приложение запустит маленький веб-сервер, который будет отвечать на адрес **http://localhost:8080/**.
Вы можете изменить порт по умолчанию на другой, указав номер порта в качестве параметра, например так:

    $ python code.py 6060

Вы также можете запускать этот скрипт как **CGI** или **FastCGI** скрипт -- он автоматически распознает подобные вещи.
На самом деле web.py использует **WSGI**, так что ваше приложение может работать с любым интерфейсом к web для
python, в том числе **scgi** и **mod_python**.

Теперь, если вы откроете свой браузер на ссылке [http://localhost:8080/](http://localhost:8080/), ваше приложение поздаровается с вами.

## Шаблоны

Написание HTML внутри кода на Python это отвратительно. Куда веселее писать на Python внутри HTML. К счастью web.py позволяет нам это делать, с легкостью.

Cоздадим каталог для наших темплейтов (назовем его... ммм... "templates"). Внутри этого каталога создадим файлик с расширением .html (скажем **index.html**). Теперь внутри это файла напиешем нормальный HTML:

    <em>Превед</em>, орлы!

Или используем специальный синтаксис темплейтов web.py, например так:

    $def with (name)    
    $if name:
        <em>Превед</em> тебе, дорогой $name.
        Твой Медвед.
    $else:
        <em>Превед</em>, орлы!

Как видите теплейты очень похожи на обычный python за исключением оператора `def with` в начале (он говорит о параметрах, которые используются в темплейте) и символа `$` расположеного в начале каждой строки с кодом. Заметьте также что web.py автоматически ескейпит все переменные, использованные в примере, таким образом, что  если по каким-либо причинам переменная `name` будет содержать HTML, то при выводе они будут заэскейплены и показаны пользователю как текст. Чтобы выключить эскейпинг используйте синтаксис `$:name` вместо `$name`.

Вернемся к коду. Под первой строкой, сразу после импортов впишите:
 
    render = web.template.render('templates/')

Это обьяснит библиотеке, где же именно искать темплейты. Теперь поменяйте `index.GET` на:

    name = 'balbes'    print render.index(name)

Зайдите на ваш сайт и присмотритесь к надписи **Превед** тебе, дорогой balbes.

(**Заметка для девелоперов:** добавьте `, cache=True` в конце вызова `render` чтобы web.py кешировал ваши темплейты.)

## Забавные URLы

Теперь измените ваш список URLов на такой:

    '/(.*)', 'index'
и поправьте определение `index.GET` на:

    def GET(self, name):

Удалите строку, устанавливающую name. Теперь если зайти просто на `/` то увидите "Превед, орлы". А вот если зайти на `/balbes` - будет куда интереснее.
Таким образом выделенное выражение **(.*)** передалось в функцию в качестве первого параметра - **name**.

## Базы Данных

Ниже строки с **web.run** впишите:

    web.config.db_parameters = dict(dbn='mysql', user='me', pw='pass', db='dbname')

конечно же вам нужно изменить эти параметры, чтобы иметь доступ к базе данных.
Создайте простую табличку, например такую:

    CREATE TABLE todo (
        id unique AUTO_INCREMENT primary key,
        title text,
        created timestam default now()
    );

и вставьте в нее строчку с примером:

    INSERT INTO todo (title) VALUES ('Заучить web.py');

В **index.py** давайте опять переделаем `index.GET`:

    def GET(self):
        todos = web.select('todo')
        print render.index(todos)

И снова вернем список листов, так чтобы 'GET /' обращался на index.
Черт, теперь выкиньте все из **index.html** и пишите сначала:
    $def with (todos)
    <ul>    $for todo in todos:
        <li id="t$todo.id">$todo.title</li>    
    </ul>
Посетив вашу уже немаленькую веб-страничку, вы увидите один элемент:  'Заучить web.py'.
Допишите в конец этого же файла:

    <form method="post" action="add">        <p>            <input type="text" name="title" />                <input type="submit" value="Add" />        </p>    </form>
Измените ваш список ссылок **urls** вот так:

    '/', 'index',
    '/add', 'add'
Обратите внимание на запятые. Помните, что в Python две строки написанные без запятых просто обьединяются.
Добавим еще один класс -  **add**:

    class add:
        def POST(self):
            i = web.input()
            n = web.insert('todo', title=i.title)
        web.seeother('./#t'+str(n))

**web.insert** возвращает идентификатор свежевставленного элемента, а команда **web.seeother** пересылает пользователя на этот новый элемент. 

Еще одна штука - вот в строчке **i = web.input** мы получили в переменной i все параметры, переданные из формы пользователем. Красиво и просто, правда?

В темпе вальса: **web.transact()** начинает транзакцию, **web.commit()** коммитит её, **web.rollback()**, что логично, откатывает её. **web.update** работает точно также как **web.insert** за исключением того, что вместо возврата нового элемента она обновляет уже сохраненный элемент по его id (или по строке, которая подставляется в WHERE запроса)

В общем это всё к чему - теперь вы можете добавлять новые элементы в список.

## Объект-хранилище

И **web.input**, и **web.query**, а также большинство других функций **web.py** возвращают в качестве результата объект-хранилище. Этот объект похож на стандартный dictionary языка python, однако позволяет обращаться к элементам не только через d['figna'], но и d.figna, что на 3 символа короче :)

## Куки

А также спамы и трояны...

Куки работают также, как и **web.input**. **web.cookies()** возвращает объект-хранилище с набором кук, пришедших от браузера. Вы можете изменять их функцией **web.setcookie(name, value, expires="")**, где name - имя куки, value - значение, а expires, соответственно, срок истечения действия куки.

И **web.input**, и **web.cookies** в качестве параметра принимают названия и пары ключ-значение. 
Например, вызвав **web.input('color', times=1)** вы поймаете ошибку, если в форме не окажется элемента **color**, или если в элементе **times** не будет единица.

Типовой паттерн использования:

    try:
        i = web.input('foo', bar=2)
    except KeyError:
        return web.badrequest()

*Пока всё, ребята. В следующий раз я раскажу вам про модуль forms.
Комментарии? Вопросы? Мнения? Предложения? мой адрес - <bobuk@justos.org>*