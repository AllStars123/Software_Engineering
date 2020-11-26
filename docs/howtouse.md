Для работы требуется `python3`, версия должна быть не менее 3.2 (тесты запускаются для версий 3.2-3.6)
Программа запускается командой `python3 minesweeper.py <входной файл> <выходной файл>(необязательный)`.
На вход нужно задать по крайней мере одно имя файла. Из этого файла будут читаться входные данные.
В нём в первой строке должно быть три числа, разделенные пробелом: высота и ширина поля, общее количество мин.
Далее идёт представление поля. На место неоткрытой ячейки ставится символ "#", на место мины "*", на место пустой ячейки "@", на место цифровой ячейки ставится само число. 
Пример можно посмотреть [здесь](../test/inputs/005). Если не нравится такое представление, то первые три символа можно заменить на свои в файле `src/config.py`.

Другое имя файла будет использоваться как выходной файл, если он не задан, то используется значение по умолчанию: `out`. Он имеет 
разный формат в зависимости от того, если ли достоверное решение. 
* Если существует достоверное решение, то на каждой строке идут две координаты, отчёт с нуля, первая координата - вертикальная. Этот список координат разделен на две части строкой с символом "-".
Первая часть - список ячеек с минами, вторая - список ячеек, в которых мины отсутствуют. Одна из частей может быть пустая, но не обе сразу. [Пример](../test/outputs/005)
* Иначе каждая строка - две координаты и значение вероятности в долях. [Пример](../test/outputs/001)

В качестве примера работы алгоритма можно взять входной файл `test/inputs/xxx`, ожидаемый выход программы можно найти в `test/outputs/xxx`

Для генерации пустого поля можно воспользоваться командой `python3 generate_empty_field.py <высота> <ширина> <число мин> <файл>(необязательный)`.
По умолчанию используется файл `field`. После работы программы остаётся только записать в файл цифровые ячейки, флажки и пустые ячейки.

Если требуется сделать несколько ходов подряд, то чтобы не перебивать руками мины, можно использовать команду `python3 mark_mines.py 
<выходной_файл_minesweeper.py> <входной_файл>(поле игры) <выходной файл>(необязательный)`. Если не задан выходной файл, то по умолчанию записывается
в файл поля игры.

*Пример*

`python3 generate_empty_field.py 16 30 99 field` создаём стандартное поля уровня Профессионал и записываем его в файл `field`

`python3 minesweeper.py field list && python3 mark_mines.py field list && cat list` одной командой генерируем список координат, 
записываем найденные ячейки с минами в файл с полем игры, затем выводим этот список, чтобы можно было отметить ячейки, в которых нет мин,
в самой игре. Если достоверного решения нет, то программа выведет список координат с вероятностями нахождения мины и попросит угадать ячейку.
Чтобы совершать последовательные ходы, можно снова использовать эту команду, только перенеся в файл с полем игры открытые ячейки.