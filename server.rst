
.. _sphinx-chapter:
   
.. meta::
    :description: WebSocket сервер для 1С. Внешний компонент (Документация). Использование Сервера websocket для 1С: Предприятие
    :keywords: server, WebSocket, rpc

.. meta::
    :http-equiv=Content-Type: text/html; charset=utf-8

=======================================
Сервер WebSocket
=======================================

Инициализация
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. index:: Новый
.. function:: Новый("AddIn.WebSocket.Server");

    Синоним: **New()**

    Инициализация внешнего компонента (Сервер).
    
    Полученный таким образом объект используется для дальнейшего взаимодействия.
    
Пример использования:

.. code-block:: bsl
   :linenos:

    Сервер = Новый("AddIn.WebSocket.Server");


Прослушивание порта
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. index:: Запустить
.. function:: Запустить(Интерфейс, Порт, Сертификат="", Пароль="")

    Синоним: **Run()**

    Захватывает порт для прослушивания и принимает в асинхронном режиме соединения и сообщения. Для получения тела сообщения используется метод ``Получить()``
    
    **Возвращаемое значение:**

    Тип: **Булево**

    Описание: Удалось ли захватить порт и начать прослушивание
    
    Параметры:

    **Интерфейс**   - **Строка** - Интерфейс для прослушивания, например ``"0.0.0.0."``
    
    **Порт**        - **Число** - Числовой порт для прослушивания, например ``8080``
    
    **Сертификат**   - **Строка** - (Необязательный) Путь (ANSI) к сертификату для организации SSL доступа (``-----BEGIN RSA PRIVATE KEY-----`` + ``-----BEGIN CERTIFICATE-----``)

    **Пароль**      - **Строка** - (Необязательный) Пароль для сертификата
    
Пример использования:

.. code-block:: bsl
   :linenos:

    Попытка
    
        Сервер.Запустить("127.0.0.1", 8098, "c:/misc/ssl/cert.pem", "c:/misc/ssl/key.pem", "1234");

    Исключение
		
        Описание = ОписаниеОшибки();

        ТекстОшибки =  Сервер.ОписаниеОшибки();
        
        ТекстОписания = Описание + ": " + ТекстОшибки;
		
        ВызватьИсключение ТекстОписания;

    КонецПопытки;

        
Завершение прослушивания
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. index:: Остановить
.. function:: Остановить()

    Синоним: **Stop()**

    Завершает фоновую обработку соединений и освобождает порт. Исключений не вызывает
    
Пример использования:

.. code-block:: bsl
   :linenos:
    
    Сервер.Остановить();	
        

Отправка сообщения
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. index:: Отправить
.. function:: Отправить(ИДКлиента, ТелоСообщения)

    Синоним: **Send()**
    
    Добавляет сообщение для фоновой отправки клиенту

    Параметры:

    **ИДКлиента** - **Число** - Порядковый номер соединения. Можно посмотреть в свойстве ``Соединения`` или при обработке входящий сообщений методом ``Принять()``
     
    **ТелоСообщения** - **Строка** - Тело отправляемого сообщения
    
Пример использования:

.. code-block:: bsl
   :linenos:
    
    ТелоСообщения = "Hello World 1C";

    ИДКлиента = 1;

    Сервер.Отправить(ИДКлиента, ТелоСообщения);	

    
Получение сообщения
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. index:: Принять
.. function:: Принять(Таймаут, ИДКлиента, Данные)
    
    Синоним: **Receive()**

    Получает принятое сообщение в фоновом режиме, если сообщений нет ждет ``Таймаут`` миллисекунд, или ждет до появления сообщения, если ``Таймаут=0``

    **Возвращаемое значение:**

    Тип: **Булево** 

    Описание: Признак принятия сообщения. Истина, если сообщение принято         

    Параметры:

    **Таймаут** - **Число** - Количество миллисекунд, на которое заблокировать вызов и ожидать сообщения. Если 0 - ждать бесконечно.
    
    **ИДКлиента** - **Число** - Идентификатор соединения с клиентом

    **Данные** - **Строка** - Полученные данные

    
Пример использования:

.. code-block:: bsl
   :linenos:
	
    Таймаут = 0;
    ИДКлиента = 1;
    Данные = "";

    // Постоянный цикл принятие сообщений
    Пока Клиент.Принять(Таймаут, ИДКлиента, Данные) Цикл
		
        Сообщить("Принят пакет данных от " + СокрЛП(ИДКлиента));
        Сообщить(Данные);

    КонецЦикла;
    
    Таймаут = 3000;

    // Ждем 3 секунды, если не принято сообщений исполнение кода продолжается
    Пока Клиент.Принять(Таймаут, ИДКлиента, Данные) Цикл
		
        Сообщить("Принят пакет данных от " + СокрЛП(ИДКлиента));
        Сообщить(Данные);

    КонецЦикла;

    Сообщить("Закончен прием сообщений");

Отключение клиента
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. index:: Отключить
.. function:: Отключить(ИДКлиента)

    Синоним: **Disconnect()**

    **Возвращаемое значение:**

    Тип: **Булево** 

    Описание: Признак успешного отключения клиента
    
    Отключает клиента от себя по идентификатору соединения

    Параметры:
    
    **ИДКлиента** - **Число** - Идентификатор соединения с клиентом
    
Пример использования:

.. code-block:: bsl
   :linenos:
    
    ИДКлиента = 2;

    Сервер.Отключить(ИДКлиента);	
        
Сервер
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. index:: Сервер
.. function:: Сервер

    Синоним: **Server**
    
    **Возвращаемое значение:**

    Тип: **Строка**

    Описание: Хранит адрес текущего интерфейса прослушивания
    
Пример использования:

.. code-block:: bsl
   :linenos:
    
    Если ЗначениеЗаполнено(Клиент.Сервер) Тогда
        Сообщить("Сейчас сервер прослушивает: " + Клиент.Сервер);
    КонецЕсли;

Порт
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. index:: Порт
.. function:: Порт

    Синоним: **Port**
    
    **Возвращаемое значение:**

    Тип: **Число**

    Описание: Хранит порт прослушивания
    
Пример использования:

.. code-block:: bsl
   :linenos:
    
    Если ЗначениеЗаполнено(Сервер.Сервер) Тогда
        Сообщить("Сейчас сервер прослушивает порт: " + Сервер.Порт);
    КонецЕсли;

Соединения
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

.. index:: Порт
.. function:: Соединения

    Синоним: **Connections**
    
    **Возвращаемое значение:**

    Тип: **Строка**

    Описание: Текущие активные соединения, ``ИД1:АдресПодключения1|ИД2:АдресПодключения2``
    
Пример использования:

.. code-block:: bsl
   :linenos:
    
    Если ЗначениеЗаполнено(Сервер.Сервер) Тогда
        Сообщить("Текущие соединения: " + Сервер.Соединения);
    КонецЕсли;