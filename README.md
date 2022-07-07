# Task7 – постановка задачи 
### Цель – освоить клиент-серверную модель взаимодействия процессов, основанную на сокетах.  
### Средство – языки Си или С++ (для тех, кто уже знаком с языком С++).  

Модель с межклиентским взаимодействием  
- Процесс-сервер:  
 1) создает "слушающий" сокет и ждет запросов на соединение от клиентов;  
 2) в бесконечном цикле – либо принимает запрос на подключение от клиента, либо  
 обрабатывает данные полученные от клиента. Обработка данных может включать в себя  
 в том числе отправку данных конкретному клиенту, отправку данных всем клиентам,  
 сохранение данных и т.п.  
 
- Процесс-клиент запрашивает у пользователя данные, отправляет их серверу (адрес сервера и  
номер порта задать в командной строке (аргументах main)), получает от сервера ответ,  
выполняет преобразования (или некие действия, например, печать на экран).  
Очевидно, что в данной модели сервер выступает в качестве управляющего узла, ему необходимо  
знать всех подключенных клиентов (а не только одного как это делает процесс-сын в базовой  
модели) и уметь понимать, чего эти клиенты хотят. В этом помогут системные функции select() или  
более современная его версия – poll(), смотрите описание в справочнике man.  

## Вариант – Калькулятор
Процесс-сервер – создает "слушающий" сокет и ждет запросов на соединение от клиентов. Приняв  
запрос, процесс-сервер создает сыновний процесс, который должен обслужить клиента и  
завершиться, а процесс-отец продолжает принимать запросы на соединение и создавать новых  
сыновей.  

Задача сына (обслуживание) – выполнять возможные команды от клиента:  
1) \+ <число> – установить число на которое сервер будет увеличивать числа для данного  
клиента (по умолчанию 1), вернуть клиенту “Ok”;  
2) <число> – запрос на увеличение числа от клиента, задача – увеличить данное число на  
заданное и вернуть клиенту;  
3) \? – получить от сервера число, на которое тот увеличивает числа для текущего клиента;  
4) \- – сообщить серверу о завершении работы, при этом сервер-сын завершается.  

Программа-клиент:  
1) запрашивает у пользователя очередную команду, отправляет ее серверу (адрес сервера  
можно задать в командной строке (передаются в main() через argv), или спросить у  
пользователя первым действием;  
2) получает от сервера ответ и печатает его на экран.  
В качестве программы клиента возможно использование утилит telnet или netcat (в разных  
системах может называться nc, netcat, ncat, pnetcat, не входит в стандарт POSIX).  

## Усложнение варианта:  
Клиентам доступны те же команды, но с некоторыми изменениями:  
1) Все клиенты обслуживаются единым сервером (а не отдельными сыновьями).  
2) Команда +<число> устанавливает глобальное для всех клиентов число, на которое будет  
увеличен любой запрос вида <число>.  
3) Сервер ждет пока к нему не придет два числа от одного и того же или нескольких клиентов  
(с помощью команды <число>) складывает эти два числа, прибавляет к ним то, что  
установлено глобально и возвращает только тем клиентам, которые участвовали в операции  
(прислали эти два числа).  

В данном варианте сервер завершает свою работу только в случае ввода (если не
указано иного):  
• команды \exit на самом сервере (от клиентов такая команда не принимается)  
• сигнала SIGINT (по нажатию ctrl+C на клавиатуре)  
• сигнала SIGSTP (по нажатию ctrl+Z на клавиатуре)  
• сигнала SIGTERM (команда kill <pid_процесса>).  
