# Интерграция бота VK
1. [Создание бота на платформе](#Создание-бота-на-платформе)
    1.1. Создание нового проекта
    1.2. Подготовка чат-бота
2. Настройка доступа в VK
3. Интеграция бота


>Чат-бот - скрипт, который получает уведомления о новых событиях из VK и обрабатывает их.

Для создания бота потребуются:
* Сообщество, от имени которого бот будет общаться с пользователями VK.
* Сервер, который будет принимать уведомления о событиях.
* Логика самого бота - скрипт, который определяет, как бот реагирует на то или иное событие.

Продумайте функциональность бота: составьте список возможных текстовых команд или событий, на которые бот должен реагировать, и соответствующие им ответные действия бота. Стоит помнить, что люди могут ошибаться, набирая команды, и присылать боту текст, отличающийся от всех учтенных вариантов - важно предщусмотреть действие по умолчанию (можно в таком случае присылать пользователю список всех поддерживаемых команд).



## Создание бота на платформе

Авторизуйтесь на платформе.
Выберите проект: иконка шестеренки > *'Проекты'*. Либо создайте новый проект.


### Создание нового проекта

Кликните иконку шестеренки > *'Проекты'* > *'Создать проект'*. Заполните поля:
* *'Имя'* - название проекта;
* *'Описание'*;
* *'Внешний репозиторий'* - активизируйте переключатель;
* *'Ссылка на репозиторий'* - вставьте ссылку;
* *'Имя пользователя'*;
* *'Пароль'*;
* *'Папка контена в репозитоии'* - по умолчанию корневаня папка репозитория *'**.**'*;
* *'Ветка'* - по умолчанию *'master'*.

Кликните *'Создать'*.

> На панели управления доступен функционал проекта: редактирование сценариев, аналитика, входящие и операторские каналы, клиенты, логи сервера.

### Подготовка чат-бота

Минимальные условия корректной работы чат-бота:
* файла `chanbot.yaml`, который содержит дескриптор проекта и лежит в корневой папке проекта;
* папки `src`, в которой лежит файл с основным сценарием работы чат-бота `main.sc` (также в этой папке могут лежать файлы с дополнительными сценариями, словари в формате `.csv` ии скрипты в формате `.js`);
* папки `test`, в которой лежат тесты в формате `.xml` для сценария работы чат-бота.

Кликните *'Редактоор сценариев'* на панели управления. Для добавления папки/файла кликните правой кнопкой мыши в боксе справа от панели управления. В правой части экрана - область редактирования файла.

Создайте файл `chanbot.yaml` и добавьте в него дескриптор проекта:

```yaml
name: hello
entryPoint:
  - main.sc
```
Таким образом, мы указали название чат-бота и точку входа в него.

Создайте папку `src`, внутри неё создайте файл `main.sc` и пропишите в нём сценарий работы чат-бота. Например, так:

``` sc
theme: /
    state: start
        q!: * *start
        a: Привет! Это было несложно, да?
        go: /

    state: hello
        q: *
        a: Тогда предлагаю ознакомиться с другими разделами нашего руководства пользователя.
```


> * Сценарий описывает переходы чат-бота из одного состояния `state` в другое. В нашем случае внутри состояния указывается ввод пользователя (под тегом `q`) и соответствующая реакция чат-бота: переход в следующее состояние (тег `go`) или ответная реплика чат-бота (тег `a`).
> * Для описания ввода полбзователя можно использовать регулярные выражения.
> * При запуске чат бота первым запросом по умолчанию посылается `/start`. Для его обработки необходимо состояние `start`. Если его не будет, то при запуске чат-бот найдет состояние, реагирующее на любой текстовый ввод, либо выведет ошибку обработки запроса.

Создайте папку `test`, внтури нее создайте файл `text.xml` и пропишите в нем тесты для сценария работы чат бота. Для нашего примера:

``` xml
<test>
    <test-case>
        <q>/start</q>
        <a state = "/start"/>
        <q>Ок</q>
        <a state = "/hello"/>
    </test-case>
</test>
```


> * Каждый `test-case` проверяет, соответствует ли реальный ответ чат-бота на конкретный пользовательский запрос ожидаемому ответу.
> * Реплика пользователя обозначается тегом `<q>`, а реакция `<a>`.
> Обычно в качестве ответа указывается состояние, в которое попадает чат-бот.Таким образом, при изменении ответа чат-бота в сценарии не нужно будет менять его в тестах. Например, в нашем тесте, мы проверям, действительно ли чат-бот при запуске поппадает в состояние `start`, а по фразе пользователя "Ок" переходит в состояние `hello`.

Кликните *'Сохранить'* в правом нижнем углу.



## Настройка доступа в VK

Для подключения чат-бота вы можете использовать любое свое сообщество VK - группу, встречу или публичную страницу.

Укажите, что в сообществе будет работать бот. Для этого перейдите *'Управление сообществом'* > *'Сообщения'* > *'Настройки для бота'* и включите пункт *'Возможности ботов'*. Далее включите сообщения в сообществе *'Управление сообществом'* > *'Сообщения'*.

Чтобы пользователи смотгли добавлять бота в беседы, необходимо включить эту возможность в настройках. Перейдите *'Управление сообществом'* > *'Собщения'* > *'Настройки для бота'*  и поставьте галочку в пункте *'Разрешать добавлять сообщество в беседы'*. После этого в сообществе появится кнопка *'Пригласить в беседу'*, открывающая модальное окно со спискм бесед.

### Получение ключа доступа

Откройте раздел *'Управление сообществом'* (*'Управление страницей'*, если публичная страница), вкладка *'Работа с API'* > *'Создать ключ доступа'*. Отметьте необходимые права доступа и подтвердите выбор.

> Ключи нельзя размещать публично! Если ключ был скомпроментирован, необходимо удщалить его из списка - после этого он станет недействителен.
