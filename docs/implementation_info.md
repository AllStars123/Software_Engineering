## Описание логики программы
Программа начинается в функции `main` в файле `minesweeper.py`. По сути она обрабатывает возможные исключения и  передаёт аргументы
командной строки функции `solve`, в которой происходит основная логика. Сначала вызывается функция `parse_game`, чтобы 
перенести информацию из входного файла в класс `Game`, с которым работает программа. Затем вызывается функция `groups_solver.solve`, 
которая пытается найти достоверное решение. Если получается это сделать, то выводятся отсортированные лексикографически сначала 
ячейки с минами, а затем ячейки, в которых мины отсутствуют. Если достоверного решения нет, то вызывается функция `get_probs`, которая
будет считать вероятности. Далее результаты сортируются сначала по вероятности, а затем по координатам, потом выводятся в файл.
Все сортировки используются только для удобства пользователя.

А теперь подробнее о каждой части. Класс `Game` содержит в себе размеры поля, само поле, общее количество мин и группы. Поле - двумерный
массив, содержащее в себе число, характеризующее ячейку. Группы - это массив групп, где группа представляет собой неупорядоченное множество (`set`)
ячеек и скорректированный вес. `set` для этой задачи подходит идеально, так как ячейки должны быть уникальными, так же нужно часто выполнять
теоретико-множественные операции. Вероятности хранятся в хэш-таблице `probs`, что очень похоже на ситуацию с группами. 
* **parse_game** 
Функция построчно считывает входной файл и заполняет игровое поле, которое изначально является двумерным массивом, состоящим из неоткрытых
ячеек. Далее мы проходимся по заполненному полю и если встречаем цифровую ячейку,
то пытаемся создать из неё группу. Это получится, если в её области будет хотя бы одна неоткрытая ячейка. Далее созданную группу добавляем в
изначально пустой массив групп. 
* **groups_solver.solve** 
Сначала для групп попарно применяем операцию, которая подробно описана в основной части. Нужно только сказать, что именно здесь `set`
показывает себя во всей красе, когда нужно и пересекать множества, и находить разности, и выяснять, является ли одно множество
подмножеством другой. Проведя несколько итераций перейдём к вычленению достоверного решения. Для этого заведём ещё два множества
`mines` и `not_mines`. Проходясь по всем группам, если встречаем группу с нулевым весом, то объединяем её ячейки с `not_mines` и 
помещаем результат в `not_mines`. Аналогично с группой, в которой количество ячеек равно весу.
* **get_probs** 
Инициализируем хэш-таблицу из групп. Корректируем значения. Всё подробно описано в основной части.

## Оценка сложности
#### По памяти
На этапе разбора файла мы храним двумерный массив размером `m * n`. Также у нас будет список групп, его размер можно оценить как 
`O(m * n)`. Действительно, количество цифровых ячеек, вокруг которых есть неоткрытые (количество групп), явно не больше общего количества ячеек.
Количество ячеек в каждой группе не более восьми. Далее в зависимости от входных данных мы будем хранить либо `mines` и `not_mines`, либо 
`probs`. Суммарное количество ячеек в `mines` и `not_mines` опять таки не больше общего, поэтому затратим `O(n * m)` памяти. Количество
элементов в таблице `probs` равно количеству неоткрытых ячеек, рядом с которыми есть цифровые. Поэтому и в этом случае затратим `O(n * m)` памяти.

Таким образом, общее количество памяти, требуемое алгоритмом равно `O(n * m)`
#### По времени
В начале аналогичная с предыдущей ситуация. Сначала тратим `O(n * m)` времени на проход по файлу, затем столько же на проход по двумерному массиву,
причём при создании группы тратим `O(1)` времени, так в области цифровой ячейки не более восьми элементов. 

После получения групп, запускаем операцию 
над группами до тех пор, пока они не перестанут изменяться. Операция в цикле перебирает все пары групп за квадратичное время. Внутри цикла
все операции константные, кроме удаления из массива, которое работает за линейное время. Казалось бы, 
что если итераций квадратичное число, а одна итерация может потребовать линейное время, то сложность одной операции будет кубической.
Но это не так. Пусть удаляется `j`-й элемент, при сравнении его с `i`-м, причём всегда `i < j`. Тогда при удалении мы перенесём влево элементы 
c индексом, большим `j`. Но это компенсируется тем, что теперь мы не будем сравнивать `j`-й элемент со всеми следующими, к тому же ещё
и не будем сравнивать элементы с индексами `i + 1, i + 2, ..., j - 1` с `j`-м. То есть при удалении элемента мы совершаем даже меньше операций,
причём это относится и к возможным последующим. Таким образом, сложность одной операции `O((n * m)^2)`, тогда общая сложность 
обработки групп будет `O((n * m)^3)` (по аналогии с методом Гаусса). 

Далее выделяем достоверное решение так же за `O(n * m)`, если оно есть. Если нет, то инициализируем хэш-таблицу, проходясь по всем ячейкам
групп. Это займёт `O(n * m)`. Далее корректируем вероятности так же за `O(n * m)` (см. основную часть). Далее сортируем либо 
`mines` и `not_mines`, либо `probs`. В обоих случаях затратим `O((n * m) * log(n * m))`

Подводя итог, алгоритм отработает за `O((n * m)^3)`
