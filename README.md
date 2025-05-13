5 задание 4 пункта - сложность 1
6 задание 3 пункта - сложность 1
7 задание 2 пункта - сложность 1
8 задание 5 пунктов - сложность 2
Суммарно 5 баллов усложнений
# Задание 1
В задании 1 была реализованна функция visual
~~~
def visual(pt: iter, color='black'):
    fig, ax = plt.subplots()
    for a in pt:
        a.set_color(color)
        ax.add_patch(a)
    ax.set_xlim([-20, 20]) #сделать правильное определение границ
    ax.set_ylim([-20, 20])
    ax.grid()
    plt.show()
~~~
Функция visual принимает на вход набор объектов pt, которые предполагаются быть графическими объектами (например, объектами из библиотеки matplotlib, такими как патчи), и параметр color, задающий цвет этих объектов по умолчанию чёрный. Внутри функции создаётся новая фигура и координатная ось с помощью plt.subplots(). 
Далее происходит цикл по каждому объекту из pt: для каждого объекта устанавливается цвет на заданный, и этот объект добавляется на ось через метод ax.add_patch(). После этого устанавливаются пределы осей ax.set_xlim([-20, 20]) и ax.set_ylim([-20, 20]), то есть область отображения ограничивается прямоугольником с координатами от -20 до 20 по обеим осям. Затем включается отображение сетки на графике с помощью ax.grid(). В конце вызывается plt.show(), который отображает окно с построенным графиком, в котором видны все добавленные объекты в указанном цвете и в заданных границах.
~~~
import matplotlib.pyplot as plt
from matplotlib.patches import Polygon
import math
import random
import numpy as np
np.set_printoptions(suppress=True)
~~~
Matplotlib.pyplot - как раз для этой части.
# Задание 2
Тут были реализованны gen_rectangle, gen_triangle, gen_hexagon и специальная функция rotate.
~~~
def gen_rectangle(w, h, s):
    startx = 0
    width, height = w, h
    counter = 0
    while True:
        if counter == 0:
            p = Polygon([[0, 0], [width, 0],
                         [width, height], [0, height]])
            startx += s
        else:
            if counter % 2 == 1:
                p = Polygon([[startx, 0], [startx+width, 0],
                            [startx+width, height], [startx, height]])
                startx *= -1
            else:
                p = Polygon([[startx, 0], [startx+width, 0],
                            [startx+width, height], [startx, height]])
                startx *= -1
                startx += s
        counter += 1
        yield p
~~~
Вывод:
~~~
rec = gen_rectangle(4, 2, 5)
recs = [next(rec) for _ in range(10)]
visual(recs)
~~~
Этот код создаёт последовательность прямоугольников, которые чередуются по расположению: первый — в начале координат, второй сдвинут вправо, третий — сдвинут влево (отрицательное значение startx), четвёртый — снова вправо, и так далее. Затем эти прямоугольники отображаются на графике с помощью функции visual.
~~~
def rotate(p_old, angle, center):
    x_c, y_c = center
    x_old, y_old = p_old
    x_old -= x_c
    y_old -= y_c
    x_new = x_old * math.cos(angle) - y_old * math.sin(angle)
    y_new = x_old * math.sin(angle) + y_old * math.cos(angle)
    return (x_new+x_c, y_new+y_c)
~~~
Функция принимает точку p_old, угол поворота angle (в радианах) и центр поворота center. Она смещает точку так, чтобы центр стал началом координат, поворачивает её на заданный угол по формуле поворота в 2D, затем возвращает точку в исходную систему координат, учитывая сдвиг центра. Результат — координаты повернутой точки.
~~~
def gen_triangle(l, s):
    startx = 0
    length = l
    counter = 0
    while True:
        if counter == 0:
            p = Polygon([[0, 0], [length, 0], rotate([length, 0], math.pi/3, [0, 0])])
            startx += s
        else:
            if counter % 2 == 1:
                p = Polygon([[startx, 0], [startx+length, 0], rotate([startx+length, 0], math.pi/3, [startx, 0])])
                startx *= -1
            else:
                p = Polygon([[startx, 0], [startx+length, 0], rotate([startx+length, 0], math.pi/3, [startx, 0])])
                startx *= -1
                startx += s
        counter += 1
        yield p
~~~
~~~
triang = gen_triangle(4, 5)
triangs = [next(triang) for _ in range(10)]
visual(triangs)
~~~
Функция генерирует бесконечную последовательность равносторонних треугольников (угол 60°) с основанием вдоль оси X. Треугольники чередуются по расположению: первый в начале координат, следующий сдвигается вправо, следующий — влево, и так далее. Для построения третьей вершины используется функция rotate, которая поворачивает точку на 60° вокруг одной из вершин основания. Полученные объекты Polygon можно визуализировать или использовать дальше.
~~~
def gen_hexagon(l, s):
    startx = 0
    length = l
    counter = 0
    while True:
        if counter == 0:
            points = [[0 + length*i, 0] for i in range(6)]
            for j in range(5):
                for i in range(5, j, -1):
                    points[i] = rotate(points[i], math.pi/3, points[j])
            p = Polygon(points)
            startx += s
        else:
            if counter % 2 == 1:
                points = [[startx + length*i, 0] for i in range(6)]
                for j in range(5):
                    for i in range(5, j, -1):
                        points[i] = rotate(points[i], math.pi/3, points[j])
                p = Polygon(points)
                startx *= -1
            else:
                points = [[startx + length*i, 0] for i in range(6)]
                for j in range(5):
                    for i in range(5, j, -1):
                        points[i] = rotate(points[i], math.pi/3, points[j])
                p = Polygon(points)
                startx *= -1
                startx += s
        counter += 1
        yield p
~~~
~~~
hex = gen_hexagon(2, 6)
hexs = [next(hex) for _ in range(10)]
visual(hexs)
~~~
Функция gen_hexagon является генератором, который бесконечно создаёт правильные шестиугольники. Она принимает параметры длины стороны l и сдвига по оси X s. В начале и при каждом шаге цикла создаётся список из шести точек, расположенных на оси X с равным интервалом length. Затем с помощью вложенных циклов и функции rotate каждая точка поворачивается на 60 градусов вокруг предыдущих точек, формируя правильный шестиугольник. Позиция по оси X для каждого следующего шестиугольника изменяется с учётом параметра сдвига s и меняет знак для чередования расположения слева и справа. Каждый сформированный шестиугольник возвращается с помощью оператора yield. Таким образом, функция последовательно выдаёт объекты шестиугольников, расположенных вдоль оси X с чередующимся смещением.
# Задание 3
Тут были реализованны tr_translate, tr_rotate,tr_symmetry, tr_homothety.
tr_translate - перенос по х у на плоскости.
tr_rotate - поворот на заданный градус через пи.
tr_symmetry - зеркальное отражение по х и у.
tr_homotety - изменение масштаба по смыслу логики homotety.
~~~
def tr_translate(polygon, x_s, y_s):
    points = polygon.xy
    res = []
    for i in range(len(points)):
        t = [points[i][0] + x_s, points[i][1] + y_s]
        res.append(t)
    return Polygon(res[:-1])
~~~
~~~
hex = gen_hexagon(2, 6)
hexs = [next(hex) for _ in range(10)]
hexs2 = []
for a in hexs:
    hexs2.append(tr_translate(a, 5, 7))
visual(hexs)
visual(hexs2)
~~~
Функция `tr_translate` сдвигает многоугольник на заданные значения по осям X и Y, создавая новый многоугольник с обновлёнными вершинами. В основном коде создаётся 10 шестиугольников через генератор `gen_hexagon`, затем создаются их копии, сдвинутые на (5, 7). Оба набора — исходные и сдвинутые — визуализируются, демонстрируя генерацию и трансляцию фигур на плоскости.
~~~
def tr_rotate(polygon, angle):
    res = []
    points = polygon.xy
    for i in range(len(points)):
        nx = points[i][0] * math.cos(angle) - points[i][1] * math.sin(angle)
        ny = points[i][0] * math.sin(angle) + points[i][1] * math.cos(angle)
        t = [nx, ny]
        res.append(t)
    return Polygon(res[:-1])
~~~
~~~
hex = gen_hexagon(2, 6)
hexs = [next(hex) for _ in range(10)]
hexs2 = []
for a in hexs:
    hexs2.append(tr_rotate(a, math.pi/2))
visual(hexs)
visual(hexs2)
~~~
Функция tr_rotate поворачивает многоугольник на заданный угол вокруг начала координат. Она берёт каждую точку многоугольника и вычисляет её новое положение после поворота. В коде создаётся 10 шестиугольников, потом для каждого из них создаётся повернутая версия на 90 градусов. После этого сначала показываются исходные шестиугольники, а потом повернутые.
~~~
def tr_symmetry(polygon, axis):
    points = polygon.xy
    res = []
    if axis == 'y':
        for i in range(len(points)):
            t = [points[i][0]*(-1), points[i][1]]
            res.append(t)
    if axis == 'x':
        for i in range(len(points)):
            t = [points[i][0], points[i][1]*(-1)]
            res.append(t)
    return Polygon(res[:-1])
~~~
~~~
hex = gen_hexagon(2, 5)
hexs = [next(hex) for _ in range(10)]
hexs2 = []
for a in hexs:
    hexs2.append(tr_symmetry(a, 'x'))
visual(hexs)
visual(hexs2)
~~~
Функция tr_symmetry отражает многоугольник относительно оси X или оси Y. Если задана ось 'y', она меняет знак у координаты X каждой вершины, если 'x' — меняет знак у координаты Y. В коде создаётся 10 шестиугольников, затем для каждого создаётся отражённая версия относительно оси X. После этого сначала отображаются исходные шестиугольники, а потом отражённые.
~~~
def tr_homothety(polygon, coef):
    res = []
    points = polygon.xy
    for i in range(len(points)):
        t = [points[i][0] * coef, points[i][1] * coef]
        res.append(t)
    return Polygon(res[:-1])
~~~
~~~
hex = gen_hexagon(2, 6)
hexs = [next(hex) for _ in range(10)]
hexs2 = []
for a in hexs:
    hexs2.append(tr_homothety(a, 0.5))
visual(hexs)
visual(hexs2)
~~~
Функция tr_homothety масштабирует многоугольник относительно начала координат с заданным коэффициентом coef. Каждая вершина умножается на этот коэффициент, уменьшая или увеличивая размер фигуры. В коде создаётся 10 шестиугольников, затем создаются их уменьшенные копии с коэффициентом 0.5. Сначала отображаются исходные шестиугольники, потом уменьшенные.
# Задание 4
Создать на основе функций данные задания:
1) три параллельных «ленты» из последовательностей полигонов, расположенных под острым углом к оси абсцисс.
2) две пересекающихся «ленты» из последовательностей полигонов, пересекающихся не в начале координат.
3) две параллельных ленты треугольников, ориентированных симметрично друг к другу.
4) последовательность четырехугольников в разном масштабе, ограниченных двумя прямыми, пересекающимися в начале координат.
~~~
n = 15
rect1 = gen_rectangle(4, 3, 5)
rects_mid = [next(rect1) for _ in range(n)]
rects_down = map(tr_translate, rects_mid, [0 for _ in range(n)], [-5 for _ in range(n)])
rects_up = map(tr_translate, rects_mid, [0 for _ in range(n)], [5 for _ in range(n)])

rects_mid = map(tr_rotate, rects_mid, [math.pi/4 for _ in range(n)])
rects_down = map(tr_rotate, rects_down, [math.pi/4 for _ in range(n)])
rects_up = map(tr_rotate, rects_up, [math.pi/4 for _ in range(n)])

rects = iter(list(rects_up) + list(rects_mid) + list(rects_down))

visual(rects)
~~~
rect1 = gen_rectangle(4, 3, 5) — генератор прямоугольников шириной 4, высотой 3, с шагом 5 по оси X.
rects_mid — список из 15 прямоугольников из генератора. rects_down — копии rects_mid, сдвинутые вниз на 5 по оси Y. rects_up — копии rects_mid, сдвинутые вверх на 5 по оси Y.

Затем все три набора прямоугольников поворачиваются на 45° (π/4).
Далее все повернутые прямоугольники объединяются в единый список, превращаются в итератор rects.
~~~
n = 20
rect1 = gen_rectangle(4, 3, 5)
rects = [next(rect1) for _ in range(n)]
rects_down = map(tr_rotate, rects, [math.pi/3 for _ in range(n)])
rects_up = map(tr_rotate, rects, [-math.pi/6 for _ in range(n)])
rects_down = map(tr_translate, rects_down, [0 for _ in range(n)], [10 for _ in range(n)])
rects_up = map(tr_translate, rects_up, [-13 for _ in range(n)], [0 for _ in range(n)])

rects = iter(list(rects_down) + list(rects_up))
visual(rects)
~~~
Сначала через генератор gen_rectangle(4, 3, 5) создаётся 20 прямоугольников.  
rects_down — эти прямоугольники поворачиваются на 60° (π/3), затем сдвигаются вверх на 10 по оси Y. rects_up — те же прямоугольники поворачиваются на -30° (-π/6), затем сдвигаются влево на 13 по оси X.После этого оба списка объединяются и преобразуются в итератор.Вызов visual(rects) отображает оба набора прямоугольников на одном графике, создавая эффект двух пересекающихся рядов с разными углами и смещениями.
~~~
n = 15
trig = gen_triangle(2.5, 5)
trigs_down = [next(trig) for _ in range(n)]
trigs_up = map(tr_symmetry, trigs_down, ['x' for _ in range(n)])
trigs_up = map(tr_translate, trigs_up, [0 for _ in range(n)], [5 for _ in range(n)])

trigs = iter(list(trigs_down) + list(trigs_up))
visual(trigs)
~~~
С помощью генератора gen_triangle(2.5, 5) создаётся 15 треугольников, которые сохраняются в trigs_down. Второй набор trigs_up создаётся отражением первого набора относительно оси X (tr_symmetry с параметром 'x'), а затем сдвигается вверх на 5 по оси Y (tr_translate). Оба набора объединяются и передаются в функцию visual для отображения. В результате на графике появляются два ряда треугольников: нижний и верхний, симметрично отражённый и смещённый вверх.
~~~
trig1 = next(gen_triangle(55, 0))
trig2 = next(gen_triangle(55, 0))

trig1 = tr_rotate(trig1, math.pi/12)
trig2 = tr_rotate(trig2, math.pi+math.pi/12)

rec = gen_rectangle(1, 100, 5)
recs = [next(rec) for _ in range(15)]
recs = [tr_translate(a, 0, -50) for a in recs]
recs = [tr_rotate(a, math.pi/4) for a in recs]
for a in recs:
    a.set_color('white')
visual([trig1, trig2]+recs)
~~~
создает два треугольника со стороной 55 единиц без смещения, после чего поворачивает первый треугольник на 15 градусов (π/12), а второй - на 195 градусов (π + π/12). Затем генерируется 15 прямоугольников размером 1×100 единиц с шагом 5 по оси X. Все прямоугольники смещаются вниз на 50 единиц по оси Y и поворачиваются на 45 градусов (π/4), после чего их цвет устанавливается на белый. Финальная композиция, состоящая из двух повернутых треугольников и набора белых прямоугольников, отображается с помощью функции visual, демонстрируя геометрический паттерн с центральными треугольниками и радиально расположенными прямоугольными элементами.
# Задание 5
Здесь я использовал такие функции как:
flt_convex_polygon, flt_square, flt_short_side, flt_point_inside.
~~~
def flt_convex_polygon(polygon):
    points = polygon.xy
    n = len(points)
    if n < 3:
        return False
    prev_cross = 0
    for i in range(n):
        p0 = points[i]
        p1 = points[(i + 1) % n]
        p2 = points[(i + 2) % n]
        dx1 = p1[0] - p0[0]
        dy1 = p1[1] - p0[1]
        dx2 = p2[0] - p1[0]
        dy2 = p2[1] - p1[1]
        cross = dx1 * dy2 - dy1 * dx2
        if cross == 0:
            continue
        if prev_cross == 0:
            prev_cross = cross
        else:
            if (cross > 0) != (prev_cross > 0):
                return False
    return True
~~~
Функция flt_convex_polygon проверяет, является ли многоугольник выпуклым. Она получает список его вершин, перебирает каждую тройку подряд и вычисляет векторное произведение между двумя последовательными сторонами. Если хотя бы одна тройка даёт векторное произведение противоположного знака по сравнению с предыдущими (то есть "заворачивается" в другую сторону), значит, многоугольник невыпуклый, и функция возвращает False. Если все векторные произведения либо одного знака, либо равны нулю (то есть точки лежат на одной прямой), то многоугольник выпуклый, и функция возвращает True. Если вершин меньше трёх, сразу возвращается False — это не многоугольник.
~~~
def flt_square(polygon, area):
    s = 0
    points = polygon.xy
    n = len(points)
    for i in range(len(points)-1):
        s += (points[i][0]*points[i+1][1])
    s += points[n-1][0]*points[0][1]
    for i in range(len(points)-1):
        s -= (points[i+1][0]*points[i][1])
    s -= points[0][0] * points[n-1][1]
    s = abs(s)/2
    return s < area
~~~
Функция flt_square определяет, меньше ли площадь данного многоугольника заданного значения area. Она вычисляет площадь по формуле Гаусса: перебирает все вершины, суммирует специальные произведения координат по определённой схеме, берёт модуль результата и делит на два, чтобы получить положительную площадь. Затем возвращает True, если эта площадь меньше area, иначе — False. Используется для фильтрации многоугольников по их площади.
~~~
def flt_short_side(polygon, length):
    points = polygon.xy
    n = len(points)
    lengths = []
    for i in range(n-1):
        lengths.append(math.sqrt(pow(points[i][0]-points[i+1][0], 2) + pow(points[i][1]-points[i+1][1], 2)))
    return min(lengths) < length
~~~
Функция flt_short_side принимает многоугольник и число length, вычисляет длины всех его сторон (между каждой парой соседних вершин), находит самую короткую из этих сторон и возвращает True, если она меньше указанного значения length, иначе — False. Используется для того, чтобы определить, есть ли у многоугольника очень короткая сторона. 
~~~
def flt_point_inside(polygon, point):
    x, y = point
    points = polygon.xy
    n = len(points)
    inside = False

    for i in range(n):
        x1, y1 = points[i]
        x2, y2 = points[(i + 1) % n]

        # Проверяем, лежит ли точка на ребре (включая вершины)
        if (x1 == x and y1 == y) or (x2 == x and y2 == y):
            return True

        # Если ребро не горизонтальное и точка лежит между y1 и y2
        if (y1 > y) != (y2 > y):
            # Вычисляем x-координату пересечения луча с ребром
            x_intersect = (y - y1) * (x2 - x1) / (y2 - y1) + x1

            # Если точка лежит на ребре (с учетом погрешности float)
            if abs(x - x_intersect) < 1e-10:
                return True

            # Если луч пересекает ребро слева от точки
            if x < x_intersect:
                inside = not inside

    return inside
~~~
Функция flt_point_inside проверяет, лежит ли точка внутри многоугольника или на его границе. Она реализует классический алгоритм «луча»: для каждой стороны многоугольника смотрит, пересекает ли горизонтальный луч, выходящий из точки вправо, это ребро. Если точка совпадает с одной из вершин или лежит прямо на каком-то ребре, функция сразу возвращает True. Если луч пересекает ребро строго слева от точки, переменная-флаг переворачивается. В итоге, если число пересечений нечётное, точка внутри многоугольника (функция вернёт True), если чётное — снаружи (False). Таким образом, функция подходит для проверки попадания точки внутрь или на границу произвольного многоугольника.
# Задание 6
 1) фильтрация фигур, созданных в рамках пункта 4.4; подобрать параметры так, чтобы на выходе было получено шесть фигур
 2) используя функции генерации из п. 2 и операции из п. 3, создать не менее 15 фигур, которые имеют различный масштаб, и выбрать из них (подбором параметра фильтрации) не более четырех фигур, имеющих кратчайшую сторону меньше заданного значения.
 3) используя функции генерации из п. 2 и операции из п. 3, создать не менее 15 фигур имеющих множество пересечений и обеспечить фильтрацию пересекающихся фигур.
 ~~~
 # Фигуры из пункта 4.4 нельзя задать с помощью написанных функций. Создадал их вручную
polygons = [Polygon([[0.083, 0.166], [0.166, 0.083],
                     [0.666, 0.333], [0.333, 0.666]]),
            Polygon([[0.416, 0.833], [0.833, 0.416],
                     [1.333, 0.666], [0.666, 1.333]]),
            Polygon([[0.75, 1.5], [1.5, 0.75],
                     [2, 1], [1, 2]]),
            Polygon([[1.0833, 2.1667], [2.1667, 1.0833],
                     [2.666, 1.333], [1.333, 2.666]])]
polygons = [tr_homothety(a, 9.95) for a in polygons]
polygons2 = [tr_rotate(a, math.pi) for a in polygons]
visual(polygons + polygons2)

area = 70
pol_filter = filter(lambda x: flt_square(x, area), polygons+polygons2)
visual(pol_filter)
~~~
Создаём 4 многоугольника — задаём их вершины (точки).
Масштабируем (делаем больше) эти многоугольники почти в 10 раз.
Поворачиваем их на 180 градусов (то есть вверх ногами).
Рисуем все многоугольники: и обычные, и повернутые.Оставляем только большие многоугольники — те, у которых площадь больше 70.
Рисуем только большие.
~~~
# Обёртка
def flt_short_side_wrap(l):
    def flt_short_side(polygon):
        points = polygon.xy
        n = len(points)
        lengths = []
        for i in range(n-1):
            lengths.append(math.sqrt(pow(points[i][0]-points[i+1][0], 2) + pow(points[i][1]-points[i+1][1], 2)))
        lengths.append(math.sqrt(pow(points[0][0]-points[n-1][0], 2) + pow(points[0][1]-points[n-1][1], 2)))
        return min(lengths) < l
    return flt_short_side

n = 20
figures = []
for i in range(n):
    t = random.randint(0, 2)
    if t == 0:
        fig = next(gen_rectangle(random.randint(1, 15), random.randint(1, 10), 0))
    if t == 1:
        fig = next(gen_triangle(random.randint(1, 10), 0))
    if t == 2:
        fig = next(gen_hexagon(random.randint(1, 15), 0))
    figures.append(fig)

z = float(input('Введите значение: '))
print('Подходящие фигуры:')
for a in filter(flt_short_side_wrap(z), figures):
    print(a.xy)
~~~
 программа случайным образом создаёт 20 геометрических фигур: прямоугольники, треугольники и шестиугольники с разными размерами. После запуска программа попросит ввести число — это максимальная допустимая длина короткой стороны у фигуры. Из всех сгенерированных фигур будут выбраны и показаны только те, у которых хотя бы одна сторона короче введённого числа; координаты вершин таких фигур будут выведены на экран. Для корректной работы нужны Python 3, а также функции для генерации фигур (gen_rectangle, gen_triangle, gen_hexagon) и класс Polygon с атрибутом .xy для хранения вершин.
~~~
rec = gen_rectangle(2, 3, 2.5)
recs = [next(rec) for _ in range(15)]
for i in range(15):
    if i % 4 == 0 or i % 4 == 1:
        recs[i] = tr_translate(recs[i], 1, 1)
        recs[i].set_color('red')
visual(recs)


# Продолжение предыдущего кода
def is_poly_in_another(poly1, poly2):
    points = poly1.xy
    return any([flt_point_inside(poly2, a) for a in points])

new_recs = []
for i in range(len(recs)):
    if len(list(filter(lambda x: is_poly_in_another(recs[i], x), recs[:i]+recs[i+1:]))) != 0:
        continue
    else:
        new_recs.append(recs[i])
visual(new_recs)
~~~
Код создаёт 15 прямоугольников, у которых размеры 2 на 3, часть из них сдвигает на (1,1) и красит в красный цвет. Сначала показываются все эти прямоугольники. Затем из списка убираются те прямоугольники, у которых хотя бы одна вершина оказалась внутри любого другого прямоугольника (то есть если хоть чуть-чуть зашёл внутрь другого), и в конце показываются только те прямоугольники, которые не пересекаются с другими и не лежат ни в одном другом.
# Задание 7
Здесь я реализовал функции: agr_origin_nearest,agr_max_side, agr_min_area, agr_perimeter, agr_area.
которые нужно применить к последовательности полигонов с помощью функции `functools.reduce`.
~~~
def tr_flt_convex_polygon_dec(func):
    def wrapper(*args):
        new = []
        for i in range(len(args)):
            if all([isinstance(a, Polygon) for a in args[i]]):
                new += list(filter(flt_convex_polygon, args[i]))
            else:
                new.append(args[i])
        return func(new)
    return wrapper
~~~
~~~
def f(polys):
    return [tr_translate(poly, 3, 3) for poly in polys]

t = gen_rectangle(3, 2, 5)
poly = [next(t) for _ in range(10)] + [Polygon([[5, 5], [10, 5], [7, 7], [5, 10]])]
recs = f(poly)
visual(recs)

@tr_flt_convex_polygon_dec
def f(polys):
    return [tr_translate(poly, 3, 3) for poly in polys]

t = gen_rectangle(3, 2, 5)
poly = [next(t) for _ in range(10)] + [Polygon([[0, 0], [5, 0], [2, 2], [0, 5]])]
recs = f(poly)
visual(recs)
~~~
Сначала объявляется декоратор, который перед вызовом функции фильтрует переданные ему списки, оставляя только выпуклые многоугольники (используя функцию flt_convex_polygon).  
Далее есть функция f, которая сдвигает каждый многоугольник из списка на (3, 3).  
Сначала функция f вызывается обычно — она просто сдвигает все многоугольники, без фильтрации, и показывает их.  
Потом эту функцию определяют заново, но уже с декоратором. Теперь перед сдвигом из списка автоматически убираются невыпуклые многоугольники, и только оставшиеся сдвигаются и показываются.  
В результате: во втором случае на экране будут только выпуклые многоугольники, сдвинутые вправо и вверх на 3.\
~~~
def tr_flt_square_coefs(area):
    def tr_flt_square_dec(func):
        def wrapper(*args):
            new = []
            for i in range(len(args)):
                if all([isinstance(a, Polygon) for a in args[i]]):
                    new += list(filter(lambda x: flt_square(x, area), args[i]))
                else:
                    new.append(args[i])
            return func(new)
        return wrapper
    return tr_flt_square_dec
~~~
~~~
def f(polys):
    return [tr_translate(poly, 3, 3) for poly in polys]

t = gen_rectangle(3, 2, 5)
poly = [next(t) for _ in range(10)] + [Polygon([[5, 5], [10, 5], [7, 7], [5, 10]])]
recs = f(poly)
visual(recs)

@tr_flt_square_coefs(7)
def f(polys):
    return [tr_translate(poly, 3, 3) for poly in polys]

t = gen_rectangle(3, 2, 5)
poly = [next(t) for _ in range(10)] + [Polygon([[0, 0], [5, 0], [2, 2], [0, 5]])]
recs = f(poly)
visual(recs) # площадь фигнюшки больше 7
~~~
Сначала определяется декоратор, который фильтрует переданные функции списки многоугольников, оставляя только те, у которых площадь больше заданного значения (например, больше 7). Функция f сдвигает все переданные ей многоугольники на (3, 3). Первый раз функция вызывается без фильтрации — сдвигаются и показываются все фигуры. Второй раз та же функция определяется с декоратором: теперь перед сдвигом и отображением из списка автоматически убираются многоугольники с площадью 7 или меньше, и только большие по площади фигуры сдвигаются и показываются.
~~~
def tr_flt_short_side_coefs(length):
    def tr_flt_short_side_dec(func):
        def wrapper(*args):
            new = []
            for i in range(len(args)):
                if all([isinstance(a, Polygon) for a in args[i]]):
                    new += list(filter(lambda x: flt_short_side(x, length), args[i]))
                else:
                    new.append(args[i])
            return func(new)
        return wrapper
    return tr_flt_short_side_dec
~~~
~~~
def f(polys):
    return [tr_translate(poly, 3, 3) for poly in polys]

t = gen_rectangle(3, 2, 5)
poly = [next(t) for _ in range(10)] + [Polygon([[5, 5], [10, 5], [7, 7], [5, 10]])]
recs = f(poly)
visual(recs)

@tr_flt_short_side_coefs(3)
def f(polys):
    return [tr_translate(poly, 3, 3) for poly in polys]

t = gen_rectangle(3, 2, 5)
poly = [next(t) for _ in range(10)] + [Polygon([[5, 5], [10, 5], [7, 7], [5, 10]])]
recs = f(poly)
visual(recs) # кратчайшая сторона фигнюшки больше чем 3
~~~
В начале определяется декоратор tr_flt_short_side_coefs(length), который позволяет автоматически фильтровать списки многоугольников, оставляя только те из них, у которых длина самой короткой стороны больше заданного значения length. Декоратор работает так: когда украшенная им функция вызывается и получает в аргументах списки многоугольников, из этих списков убираются все фигуры, у которых хотя бы одна сторона короче или равна length. Далее объявляется функция f, которая просто сдвигает все многоугольники на (3, 3) и возвращает их. Сначала функция вызывается без декоратора — сдвигаются и отображаются все фигуры, независимо от их коротких сторон. Затем функция определяется заново, но уже с декоратором, который фильтрует входные многоугольники: теперь сдвигаются и отображаются только те фигуры, у которых каждая сторона длиннее 3. В обоих случаях многоугольники берутся из генератора прямоугольников, а также к ним добавляется один дополнительный четырёхугольник с нестандартными координатами. В итоге: без декоратора показываются все фигуры, а с декоратором — только те, у которых самая короткая сторона больше 3.
~~~
def tr_flt_point_inside_coefs(ps):
    def tr_flt_point_inside_dec(func):
        def wrapper(*args):
            new = []
            for i in range(len(args)):
                if all([isinstance(a, Polygon) for a in args[i]]):
                    new += list(filter(lambda x: flt_point_inside(x, ps), args[i]))
                else:
                    new.append(args[i])
            return func(new)
        return wrapper
    return tr_flt_point_inside_dec
~~~
~~~
def f(polys):
    return [tr_translate(poly, 3, 3) for poly in polys]

t = gen_rectangle(3, 2, 5)
poly = [next(t) for _ in range(10)] + [Polygon([[5, 5], [10, 5], [7, 7], [5, 10]])]
recs = f(poly)
visual(recs)

@tr_flt_point_inside_coefs([0.5, 0.5])
def f(polys):
    return [tr_translate(poly, 3, 3) for poly in polys]

t = gen_rectangle(3, 2, 5)
poly = [next(t) for _ in range(10)] + [Polygon([[5, 5], [10, 5], [7, 7], [5, 10]])]
recs = f(poly)
visual(recs) # только этот прямоугольник изначально содержал точку (0.5, 0.5)
~~~
В этом коде создается декоратор tr_flt_point_inside_coefs(ps), который фильтрует списки многоугольников, оставляя только те фигуры, внутри которых находится заданная точка ps (например, [0.5, 0.5]). Когда функция с этим декоратором вызывается и получает в аргументах список многоугольников, она пропускает их через фильтр: остается только те, для которых функция flt_point_inside(x, ps) возвращает True, то есть точка ps лежит внутри многоугольника. Затем определена функция f, которая просто сдвигает все фигуры на (3, 3). В первом случае функция вызывается без декоратора: сдвигаются и показываются все 10 прямоугольников (созданных генератором) и один дополнительный четырехугольник. Во втором случае эта же функция определяется с декоратором, который оставляет только те фигуры, внутри которых изначально была точка [0.5, 0.5]. В результате после применения декоратора и вызова функции отображаются только те многоугольники, которые содержали указанную точку (например, только один из прямоугольников), и к ним применяется сдвиг.
~~~
def tr_translate_coefs(xs=0, ys=0):
    def tr_translate_dec(func):
        @functools.wraps(func)
        def wrapper(*args):
            new = []
            for i in range(len(args)):
                if isinstance(args[i], Polygon):
                    new.append(tr_translate(args[i], xs, ys))
                else:
                    new.append(args[i])
            return func(*new)
        return wrapper
    return tr_translate_dec
~~~
~~~
def f(poly, number):
    return [tr_translate(poly, 3*i, 3*i) for i in range(number)]

poly = Polygon([[0, 0], [0, 2], [2, 2], [2, 0]])
recs = f(poly, 5)
visual(recs)

@tr_translate_coefs(-6, -6)
def f(poly, number):
    return [tr_translate(poly, 3*i, 3*i) for i in range(number)]

poly = Polygon([[0, 0], [0, 2], [2, 2], [2, 0]])
recs = f(poly, 5)
visual(recs) # сместились, так как декоратор сместил полигоны на -5 -5
~~~
В этом коде сначала определяется декоратор tr_translate_coefs(xs, ys), который при вызове функции автоматически смещает (сдвигает) все переданные ей многоугольники (объекты Polygon) на заданные значения по x и y, прежде чем функция начнёт свою работу. Далее есть функция f, которая создает список копий исходного многоугольника, каждый раз сдвигая его на (3i, 3i), где i — номер копии, всего создаётся столько копий, сколько задано в аргументе number. Сначала функция вызывается без декоратора — фигуры просто идут по диагонали вправо и вверх. Затем функция определяется заново, но уже с декоратором, который до вызова самой функции смещает исходный многоугольник сразу на (-6, -6), так что теперь все фигуры на экране оказываются сдвинуты левее и ниже, чем в первом случае. В результате: без декоратора копии многоугольника идут по диагонали от (0,0) вверх и вправо, а с декоратором — всё то же самое, только стартовая точка всех фигур дополнительно смещена на (-6,-6).
~~~
def tr_rotate_coefs(angle=2):
    def tr_rotate_dec(func):
        @functools.wraps(func)
        def wrapper(*args):
            new = []
            for i in range(len(args)):
                if isinstance(args[i], Polygon):
                    new.append(tr_rotate(args[i], angle))
                else:
                    new.append(args[i])
            return func(*new)
        return wrapper
    return tr_rotate_dec
~~~
~~~
def f(poly, number):
    return [tr_translate(poly, 3*i, 3*i) for i in range(number)]

poly = Polygon([[0, 0], [0, 2], [2, 2], [2, 0]])
recs = f(poly, 5)
visual(recs)

@tr_rotate_coefs(math.pi/3)
def f(poly, number):
    return [tr_translate(poly, 3*i, 3*i) for i in range(number)]

poly = Polygon([[0, 0], [0, 2], [2, 2], [2, 0]])
recs = f(poly, 5)
visual(recs)
~~~
В этом коде создаётся декоратор tr_rotate_coefs(angle), который при вызове функции автоматически вращает все переданные ей объекты Polygon на заданный угол (по умолчанию 2 радиана, но можно указать любой, например, math.pi/3). Сначала объявляется функция f, которая создаёт список из нескольких копий многоугольника, каждую сдвигая по диагонали на (3i, 3i). Первый раз функция вызывается без декоратора — в итоге отображаются 5 одинаковых многоугольников, просто идущих по диагонали. Затем функция определяется заново, но уже с декоратором, который сначала поворачивает исходный многоугольник на угол math.pi/3 (60 градусов), и только потом функция создает и сдвигает его копии по диагонали. В результате: без декоратора на экране появляются 5 обычных прямоугольников, идущих по диагонали, а с декоратором — 5 повернутых на 60 градусов прямоугольников, тоже идущих по диагонали.
~~~
def tr_symmetry_coefs(axis='y'):
    def tr_symmetry_dec(func):
        @functools.wraps(func)
        def wrapper(*args):
            new = []
            for i in range(len(args)):
                if isinstance(args[i], Polygon):
                    new.append(tr_symmetry(args[i], axis))
                else:
                    new.append(args[i])
            return func(*new)
        return wrapper
    return tr_symmetry_dec
    ~~~
    ~~~
    def f(poly, number):
    return [tr_translate(poly, 3*i, 3*i) for i in range(number)]

poly = Polygon([[0, 0], [0, 2], [2, 2], [2, 0]])
recs = f(poly, 5)
visual(recs)

@tr_symmetry_coefs('x')
def f(poly, number):
    return [tr_translate(poly, 3*i, 3*i) for i in range(number)]
poly = Polygon([[0, 0], [0, 2], [2, 2], [2, 0]])
recs = f(poly, 5)
visual(recs)
~~~
В этом коде создаётся декоратор tr_symmetry_coefs(axis), который автоматически отражает (делает симметричными) все переданные функции многоугольники относительно заданной оси ('x' или 'y'). Сначала объявляется функция f, которая создаёт список из нескольких копий многоугольника, каждую сдвигает по диагонали на (3i, 3i), и возвращает этот список. Первый запуск функции — без декоратора, поэтому на экране появляются 5 одинаковых прямоугольников, сдвинутых по диагонали от исходной позиции. Затем функция определяется заново, но уже с декоратором, который сначала отражает исходный многоугольник относительно оси X, а потом уже создает и сдвигает его копии по диагонали. В результате: без декоратора видны обычные прямоугольники, а с декоратором — их зеркальное отражение относительно оси X, тоже расположенное по диагонали.
~~~
def tr_homothety_coefs(coef=1):
    def tr_homothety_dec(func):
        @functools.wraps(func)
        def wrapper(*args):
            new = []
            for i in range(len(args)):
                if isinstance(args[i], Polygon):
                    new.append(tr_homothety(args[i], coef))
                else:
                    new.append(args[i])
            return func(*new)
        return wrapper
    return tr_homothety_dec
~~~
~~~
def f(poly, number):
    return [tr_translate(poly, 3*i, 3*i) for i in range(number)]

poly = Polygon([[0, 0], [0, 2], [2, 2], [2, 0]])
recs = f(poly, 5)
visual(recs)

@tr_homothety_coefs(2)
def f(poly, number):
    return [tr_translate(poly, 3*i, 3*i) for i in range(number)]
poly = Polygon([[0, 0], [0, 2], [2, 2], [2, 0]])
recs = f(poly, 5)
visual(recs)
~~~
# Задание 8
Тут я использовал такие функции как:
agr_origin_nearest, agr_area, agr_max_side, agr_min_area
~~~
def area(polygon):
    s = 0
    points = polygon.xy
    n = len(points)
    for i in range(len(points)-1):
        s += (points[i][0]*points[i+1][1])
    s += points[n-1][0]*points[0][1]
    for i in range(len(points)-1):
        s -= (points[i+1][0]*points[i][1])
    s -= points[0][0] * points[n-1][1]
    s = abs(s)/2
    return s

def agr_area(t, polygon):
    return t + area(polygon)
~~~
~~~
x = gen_rectangle(4, 3, 0)
recs = [0] + [next(x) for _ in range(10)]
functools.reduce(agr_area, recs)
~~~
Функция area вычисляет площадь многоугольника по его вершинам с помощью формулы Гаусса (шнуровой формулы): берёт список точек, последовательно перемножает и суммирует/вычитает координаты по специальной схеме, берёт модуль и делит на два — в результате возвращается положительная площадь. Функция agr_area нужна для аккумулирования общей площади: она принимает число и многоугольник, вычисляет площадь многоугольника и прибавляет её к числу (например, чтобы с помощью reduce посчитать сумму площадей многих фигур). Вторая часть кода
создаёт генератор прямоугольников размером 4 на 3, берёт из него 10 прямоугольников, добавляет в начало списка ноль (0), а затем с помощью functools.reduce и функции agr_area аккумулирует (суммирует) все их площади, начиная с нуля — в итоге получается сумма площадей всех 10 прямоугольников.
~~~
def origin_nearest(polygon):
    p = polygon.xy
    return min(p, key=lambda a: np.sqrt(a[0]*a[0] + a[1]*a[1]))

def agr_origin_nearest(polygon1, polygon2):
    m1 = origin_nearest(polygon1)
    m2 = origin_nearest(polygon2)
    if m1 <= m2:
        return polygon1
    else:
        return polygon2
~~~
Функция origin_nearest для заданного многоугольника находит его вершину, которая ближе всего к началу координат (0,0): она перебирает все точки и ищет ту, у которой расстояние до начала минимально. Функция agr_origin_nearest принимает два многоугольника, находит в каждом ближайшую к началу координат вершину и сравнивает их расстояния: тот многоугольник, у которого эта ближайшая вершина ближе, возвращается из функции. Такой подход можно использовать, например, с функцией reduce, чтобы из списка многоугольников найти тот, у которого хотя бы одна вершина максимально близка к (0,0).
~~~
def max_side(polygon):
    p = polygon.xy
    n = len(p)
    dists = []
    for i in range(n-1):
        dists.append(math.sqrt(pow(p[i][0]-p[i+1][0], 2) + pow(p[i][1]-p[i+1][1], 2)))
    dists.append(math.sqrt(pow(p[n-1][0]-p[0][0], 2) + pow(p[n-1][1]-p[0][1], 2)))
    return max(dists)

def agr_max_side(polygon1, polygon2):
    m1 = max_side(polygon1)
    m2 = max_side(polygon2)
    if m1 >= m2:
        return polygon1
    else:
        return polygon2
~~~
Функция max_side находит длину самой длинной стороны у данного многоугольника: она перебирает все пары соседних вершин, вычисляет расстояния между ними, а также учитывает сторону между последней и первой вершиной, и возвращает максимальное из этих расстояний. Функция agr_max_side сравнивает две фигуры: вычисляет у каждой максимальную сторону и возвращает тот многоугольник, у которого самая длинная сторона больше или равна (если равны — первый из них). Такой подход полезен, например, чтобы с помощью reduce найти в списке многоугольников тот, у которого самая длинная сторона самая большая.
~~~
def min_area(polygon):
    s = 0
    points = polygon.xy
    for i in range(len(points)-1):
        s += (points[i][0]*points[i+1][1])
    s += points[n-1][0]*points[0][1]
    for i in range(len(points)-1):
        s -= (points[i+1][0]*points[i][1])
    s -= points[0][0] * points[n-1][1]
    s = abs(s)/2
    return s

def agr_min_area(polygon1, polygon2):
    m1 = min_area(polygon1)
    m2 = min_area(polygon2)
    if m1 <= m2:
        return polygon1
    else:
        return polygon2
~~~
Функция min_area вычисляет площадь многоугольника по его вершинам с помощью формулы Гаусса (шнуровой формулы): она последовательно суммирует специальные выражения от координат соседних вершин, а в конце берёт модуль и делит на два, чтобы получить правильную (положительную) площадь. Функция agr_min_area сравнивает площади двух многоугольников: она вычисляет площадь у каждого с помощью min_area и возвращает тот многоугольник, у которого площадь меньше или равна (если равны — первый из них). Этот подход можно использовать, например, вместе с reduce, чтобы среди списка многоугольников найти тот, у которого площадь минимальна. 
~~~
def dist(p1, p2):
    return math.sqrt(pow(p1[0]-p2[0], 2) + pow(p1[1]-p2[1], 2))
def perimeter(polygon):
    res = 0
    points = polygon.xy
    for i in range(len(points)-1):
        res += dist(points[i], points[i+1])
    res += dist(points[0], points[-1])
    return res
def agr_perimeter(t, polygon):
    return t + perimeter(polygon)
~~~
~~~
x = gen_rectangle(4, 3, 0)
recs = [0] + [next(x) for _ in range(10)]
functools.reduce(agr_perimeter, recs)
~~~
Функция dist считает расстояние между двумя точками на плоскости. Функция perimeter находит периметр многоугольника: она суммирует расстояния между каждой парой соседних вершин и добавляет расстояние между первой и последней вершиной, чтобы замкнуть контур. Функция agr_perimeter нужна для аккумулирования суммы периметров — она просто к числу прибавляет периметр очередного многоугольника. Затем создаётся генератор прямоугольников со сторонами 4 и 3, из него берётся 10 прямоугольников, а в начало списка добавляется 0. С помощью functools.reduce и функции agr_perimeter суммируются периметры всех 10 прямоугольников, и на выходе получается общее значение периметра для всех этих фигур.
