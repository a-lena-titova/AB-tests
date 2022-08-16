# AB-tests
## В этом репозитории хранятся задания по теме "А/В-тесты" из симулятора аналитика Karpov Courses

⚡ Предыстория:
Мы (аналитики) работаем в некой социальной сети, в которой есть лента новостей и мессенджер. К нам пришла команда ML, которая сделала два новых алгоритма рекомендаций новостей в ленте. Естественно, ожидается, что новые алгоритмы сделают пользователей счастливее (то есть удлинится LTV, повысятся денежные конверсии и т.д.), а продукт удобнее/приятнее в использовании. Мы как аналитики должны проверить эту гипотезу. В качестве ключевой метрики используем CTR.

### 🎓 Задание 5.1. АА-тест
Для начала проверяем, что система сплитования работает корректно и ключевая метрика CTR не отличается между 0 и 1 группой не только в конкретно нашем АА-тесте, но и в целом. Для этого мы будем многократно извлекать подвыборки с повторениями из наших данных, проводить t-test, и в конце посмотрим, в каком проценте случаев нам удалось отклонить нулевую гипотезу.

Итак, что конкретно мы делаем: у нас есть данные АА-теста с '2022-05-17' по '2022-05-23'. Сделаем симуляцию, как будто мы провели 10000 АА-тестов. На каждой итерации сформируем подвыборки с повторением в 500 юзеров из 2 и 3 экспериментальной группы. Проводим сравнение этих подвыборок t-testом. Строим гистограмму распределения получившихся 10000 p-values. Считаем, какой процент p values оказался меньше либо равен 0.05.

Результат: ложноположительных результатов получилось несколько больше ожидаемых 5%, но в пределах погрешности. Поэтому на первый взгляд по проведенному АА-тесту кажется, что наша система сплитования работает корректно.

### 🎓 Задание 5.2. АВ-тест
Пришло время проанализировать результаты эксперимента, который мы провели вместе с командой дата сайентистов. Эксперимент проходил с 2022-05-24 по 2022-05-30 включительно. Для эксперимента были задействованы 2 и 1 группы. В группе 2 был использован один из новых алгоритмов рекомендации постов, группа 1 использовалась в качестве контроля. Основная гипотеза заключается в том, что новый алгоритм во 2-й группе приведет к увеличению CTR. Наша задача — проанализировать данные АB-теста. 

Итак, что конкретно мы делаем: сравниваем CTR в двух группах разными методами анализа (используем t-тест, Пуассоновский бутстреп, тест Манна-Уитни, t-тест на сглаженном ctr (α=5), а также t-тест и тест Манна-Уитни поверх бакетного преобразования). Сравниваем данные этими тестами. Описываем, почему тесты сработали так как сработали. Пишем рекомендацию, будем ли мы раскатывать новый алгоритм на всех новых пользователей или все-таки не стоит.

Результат: во 2 группе, где был использован новый алгоритм рекомендации постов, получилось бимодальное распределение CTR c 2 "пиками". Тест МаннаУитни, Пуассоновский бутстреп, а также проведенные поверх сглаженного CTR и бакетного преобразования тест МаннаУитни и t-тест показали отличия в CTR. В таргет группе CTR стал ниже, чем в контрольной. Соответсвенно, наша рекомендация -  не раскатывать алгоритм на всех пользователей. Во-первых, потому что СTR стал ниже, а это не то, чего мы добивались. Во-вторых, стоит также разобраться, почему CTR стал бимодальным. Возможно, для части пользователей алгоритм все-таки работает, в то время как для других - нет.

### 🎓 Задание 5.3. АВ-тест по метрике линеаризованных лайков
В 2018-м году исследователи из Яндекса разработали классный метод анализа тестов над метриками-отношениями вида  𝑥/𝑦. У нас ключевая метрика CTR, т.е. отношение вида likes/clicks. Идея метода заключается в следующем: вместо того, чтобы заталкивать в тест «поюзерные» CTR, можно сконструировать другую метрику и анализировать ее, но при этом гарантируется (в отличие от сглаженного CTR), что если тест на этой другой метрике «прокрасится» и увидит изменения, значит изменения есть и в метрике исходной (то есть в лайках на пользователя и в пользовательских CTR).

Итак, что конкретно мы делаем: считаем общий CTR в контрольной группе. Считаем в обеих группах поюзерную метрику линеаризованных лайков. После чего сравним  t-тестом отличия в группах по метрике линеаризованных лайков. 

Результат: Проведя AB-тест по метрике линеаризованных лайков между группами 1 и 2, t-тест показал отличие между группами. P-value меньше, чем в обычном t-тесте по CTR, который отличия не показал. В группах 0 и 3 оба t-теста показали отличие между группами. P-value по метрике линеаризованных лайков меньше, чем в обычном t-тесте по CTR. Метод работает, чувствительность теста выросла!
