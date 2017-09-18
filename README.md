http://rcppe.ru/projects/iu/Автоматическая-развиваемая-информационная-система-на-основе-технологий-СУБД

API to entry points for automatic resolved information system based on DBMS technologies

It is proposed to implement an example of the IP implemented on the following principles: 1) a finite state machine. 2) event-oriented programming the internal logic (i.e. stored procedures). 3) trigger response with the environment (table SpeechJournal). The analysis of the vocabulary it is proposed to produce functional essential principle, dictionary of IP to fill in from scratch. Offer a specific format of database and tables, and ready-made function to create them on the database server MSSQL Server 2014 and other.



Note: the field ConformityPercentage must be real and lie in the interval [-1 .. 0 .. 1]. Where 1 means a complete synonym for connection, -1 is the complete opposite, 0 is the value of this when the system is not yet known. Also provided is null in order to allow the system to reset mistakenly connection.



Authorship and source code remains with the developer. When writing scientific articles necessarily quoting author (Shaikhutdinov Albert).

Пример алгоритма для формирования оценки от 0.0 до 5.0:
1. Поля для ввода очень условны, поэтому сразу объединяем всё, что пользователь ввёл в предложенные поля для отзывов (Comment + CommentNegative + CommentPositive).
2. Знаки пунктуации пользователи вводят не всегда правильно, к тому же уже есть много решений по проверке и расстановке знаков препинания по правилам языка , поэтому пока вырежем из строки все знаки препинания (любой знак кроме буквы алфавита заменяем пробелом, после этого все подряд идущие пробелы заменяем на один пробел).
3. Выставляем первичную оценку по каждому отзыву по конкретному товару:  принимаем выставленную пользователем оценку если есть (формируем связь с оценкой пользователя X.Y (напр. 4.7) и ставим ConformityPercentage = 1), либо ставим 0 (при вычислении поле  будет вещественного типа и лежать в интервале (-1 ,, 0 ,, 1), Где 1 - означает полный синоним для связи с возможной оценкой (оценка может быть от 0,0 до 5,0, т,е, 51 возможная оценка рейтинга, поэтому добавляем 51 связь товара с оценкой и устанавливаем по ним ConformityPercentage = 0), -1 - полный антоним, 0 - значение этой связи системе пока не известно, Также предусмотрено значение null для того, чтобы система могла обнулить ошибочно сформированнную связь). Прим.: субд получает комментарии последовательно по возрастанию поля date.
4. При появлении связи с ConformityPercentage = 1 автоматически запускаем процедуру СУБД по формированию связей Слово-К-Отзыву, на каждое слово устанавливаем поле ConformityPercentage = Кол-во таких слов/ (Кол-во слов в отзыве+Кол-во таких слов), и Слово-К-Оценке где устанавливаем такое же значение или вычисляем поле ConformityPercentage если такая связь уже есть в таблицах Known..OneToOne и Known..OneToMany.
5. При появлении связи с ConformityPercentage = 0 автоматически запускаем процедуру СУБД по анализу уже имеющихся связей и корректировке полей ConformityPercentage != 1 в таблицах Known..OneToOne и Known..OneToMany.
6. После окончания ввода данных и соответствующих триггерных процедур делаем финальную оценку рейтингов по каждой строке отзывов, оценка которых не введена (т.е. отзывы на товары, по которым нет связей с какой-либой оценкой от 0.0 до 5.0 с ConformityPercantage = 1). По каждому товару мы имеем 51 связь с оценкой от 0.0 до 5.0, где поле  ConformityPercentage будет вещественного типа и лежать в интервале (-1 .. 0 .. 1). Здесь я использовал самый простой алгоритм: расположил по горизонтали 51 столбец, и нарисовал прямоугольники с высотой ConformityPercentage по каждой оценке, и выбрал такую точку по горизонтали, где сумма площадей прямоугольников слева от этой точки равна сумме площадей прямоугольников справа от этой точки. В качестве финальной оценки принимаем ближайшее число X.Y
