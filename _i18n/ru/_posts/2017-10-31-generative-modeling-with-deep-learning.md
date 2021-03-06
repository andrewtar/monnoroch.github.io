---
layout: post
title: Вероятностная интерпретация классических моделей машинного обучения
date: '2017-10-31 00:20:00 +0300'
categories: ru posts
---

Этой статьей я начинаю серию, посвященную генеративным моделям в машинном обучении. Мы посмотрим на классические задачи машинного обучения, определим, что такое генеративное моделирование, посмотрим на его отличия от классических задач машинного обучения, взглянем на существующие подходы к решению этой задачи и погрузимся в детали тех из них, что основаны на обучении глубоких нейронных сетей.
Но прежде, в качестве введения, мы посмотрим на классические задачи машинного обучения в их вероятностной постановке.

## Классические задачи машинного обучения

Две классические задачи машинного обучения &mdash; это классификация и регрессия. Давайте посмотрим ближе на каждую из них. Рассмотрим постановку обеих задач и простейшие примеры их решения.

## Классификация

Задача классификации &mdash; это задача присвоения меток объектам. Например, если объекты &mdash; это фотографии, то метками может быть содержание фотографий: содержит ли изображение пешехода или нет, изображен ли мужчина или женщина, какой породы собака изображена на фотографии. Обычно есть набор взаимоисключающих меток и сборник объектов, для которых эти метки известны. Имея такую коллекцию данных необходимо автоматически расставлять метки на произвольных объектах того же типа, что были в изначальной коллекции. Давайте формализуем это определение.
Допустим, есть множество объектов $X$. Это могут быть точки на плоскости, рукописные цифры, фотографии или музыкальные произведения. Допустим также, что есть конечное множество меток $Y$. Эти метки могут быть пронумерованы. Мы будем отождествлять метки и их номера. Таким образом $Y=\\{red, green, blue\\}$ в нашей нотации будет обозначаться как $Y=\\{1, 2, 3\\}$. Если $Y=\\{0, 1\\}$, то задача называется задачей бинарной классификации, если меток больше двух, то обычно говорят, что это просто задача классификации. Дополнительно, у нас есть входная выборка $D=\\{(x\_i, y\_i), x\_i \\in X, y\_i \\in Y, i=\\overline{1,N}\\}$. Это те самые размеченные примеры, на которых мы и будем обучаться проставлять метки автоматически. Так как мы не знаем классов всех объектов точно, мы считаем, что класс объекта &mdash; это случайная величина, которую мы для простоты тоже будем обозначать $y$. Например, фотография собаки может классифицироваться как собака с вероятностью 0.99 и как кошка с вероятностью 0.01. Таким образом, чтобы классифицировать объект, нам нужно знать условное распределение этой случайной величины на этом объекте $p(y|x)$.

Задача нахождения $p(y|x)$ при данном множестве меток $Y$ и данном наборе размеченных примеров $D=\\{(x\_i, y\_i), x\_i \\in X, y\_i \\in Y,i=\\overline{1,N}\\}$ называется задачей классификации.

## Вероятностная постановка задачи классификации

Чтобы решить эту задачу, удобно переформулировать ее на вероятностном языке. Итак, есть множество объектов $X$ и множество меток $Y$. $\xi: \Omega \to X$ &mdash; случайная величина, представляющая собой случайный объект из $X$. $\eta: \Omega \to Y$ &mdash; случайная величина, представляющая собой случайную метку из $Y$. Рассмотрим случайную величину $(\xi,\eta): \Omega \to (X, Y)$ с распределением $p(x, y)$, которое является совместным распределением объектов и их классов. Тогда, размеченная выборка &mdash; это сэмплы из этого распределения $(x\_i, y\_i) \sim p(x, y)$. Мы будем предполагать, что все сэмплы независимо и одинаково распределены (i.i.d в англоязычной литературе).

Задача классификации теперь может быть переформулирована как задача нахождения $p(x|y)$ при данном сэмпле $D=\\{(x\_i, y\_i) \sim p(x, y), i=\\overline{1,N}\\}$.

## Классификация двух нормальных распределений

Давайте посмотрим, как это работает на простом примере. Положим $X=R$, $Y=\\{0, 1\\}$, $p(x|y=0)=N(x; \mu\_0,\sigma\_0)$, $p(x|y=1)=N(x; \mu\_1,\sigma\_1)$, $p(y=0)=p(y=1)=1/2$. То есть, у нас есть две гауссианы, из которых мы равновероятно сэмплируем данные и нам нужно, имея точку из $R$, предсказать, из какой гауссианы она была получена.

<center> {% include image_with_caption.html url="/images/posts/2017-10-31-generative-modeling-with-deep-learning/graph_1.png" description="Рис. 1. Плотности распределения $p(x|y=1)$ и $p(x|y=0)$." width=520 %} </center>

Так как область определения гауссианы &mdash; вся числовая прямая, очевидно, что эти графики пересекаются, а значит, есть такие точки, в которых плотности вероятности $p(x|y=0)$ и $p(x|y=1)$ равны.

Найдем условную вероятность классов:

\\begin{equation}
    p(y|x)=\\frac{p(x,y)}{p(x)}=\\frac{p(x|y)p(y)}{\\displaystyle\\sum_{l \in Y}{p(x|l)p(l)}}=\\{p(y)=\\frac{1}{2}\\}=
    \\frac{p(x|y)}{\\displaystyle\\sum\_{l \\in Y}{p(x|l)}}
\\end{equation}

Т.е.
\\begin{equation}
    p(y=i|x)=\\frac{N(x;\mu\_i, \sigma\_i)}{\\displaystyle\sum_{l \in Y}{N(x;\mu\_l, \sigma\_l)}}
\\end{equation}

Вот так будут выглядеть график плотности вероятностей $p(y=1|x)$:

<center> {% include image_with_caption.html url="/images/posts/2017-10-31-generative-modeling-with-deep-learning/graph_2.png" description="Рис. 2. Плотности распределения $p(x|y=1)$, $p(x|y=0)$ и $p(y=1|x)$. $p(y=1|x)=1/2$ там, где две гауссианы пересекаются. " width=520 %} </center>

Видно, что близко к модам гауссиан уверенность модели в принадлежности точки конкретному классу очень высока (вероятность близка к нулю или единице), а там, где графики пересекаются модель может только случайно угадывать и выдает $p(x|y=1)=p(x|y=0)=1/2$.

## Метод максимизации правдоподобия

Большая часть практических задач не может быть решена вышеописанным способом, так как $p(x|y)$ обычно не задано явно. Вместо этого обычно имеется набор данных $D=\\{(x\_i, y\_i) \sim p(x, y), i=\\overline{1,N}\\}$ с некоторой неизвестной совместной плотностью распределения $p(x, y)$. В таком случае для решения задачи используется [метод максимального правдоподобия](https://en.wikipedia.org/wiki/Maximum_likelihood_estimation). Формальное определение и обоснование метода можно найти в вашей любимой книге по статистике или по ссылке выше, а в данной статье я опишу его интуитивный смысл.

Принцип максимизации правдоподобия говорит, что если есть некоторое неизвестное распределение $p(x)$, из которого есть набор сэмплов $D=\\{x\_i \sim p(x), i=\\overline{1,N}\\}$, и некоторое известное параметрическое семейство распределений $q(x|\theta)$, то для того, чтобы $q(x|\theta)$ максимально приблизило $p(x)$, нужно найти такой вектор параметров $\theta$, который максимизирует совместную вероятность данных (правдоподобие) $q(x\_1,\\dotsc, x\_N|\theta)$, которое еще называют правдоподобием данных. Доказано, что при разумных условиях эта оценка является состоятельной и несмещенной оценкой истинного вектора параметров. Если сэмплы выбраны из $p(x)$, то есть данные i.i.d., то совместное распределение распадается на произведение распределений:

\\begin{equation}
    \\arg\\ \\max\_{\theta} q(x\_1, \\dotsc, x\_N|\theta)=\\arg\\ \\max\_{\theta} \\prod\_{i=1..N} q(x\_i|\theta)
\\end{equation}

Логарифм и умножение на константу &mdash; монотонно возрастающие функции и не меняют положений максимумов, потому совместную плотность можно внести под логарифм и умножить на $\\frac1N$:

\\begin{equation}
    \\arg\\ \\max\_{\theta} \\prod\_{i=1..N} q(x\_i|\theta)=
    \\arg\\ \\max\_{\theta} \\frac{1}{N}\\log\\prod\_{i=1..N} q(x\_i|\theta)=
\\end{equation}
\\begin{equation}
    =\\arg\\ \\max\_{\theta} \\frac{1}{N}\\sum\_{i=1..N} \\log q(x\_i|\theta)
\\end{equation}

Последнее выражение, в свою очередь, является несмещенной и состоятельной оценкой ожидаемого логарифма правдоподобия:

\\begin{equation}
    \\arg\\ \\max\_{\theta} \\frac{1}{N}\\sum\_{i=1..N} \\log q(x_i|\theta)=
    \\arg\\ \\max\_{\theta} \\mathbb E\_{x\sim p(x)} \\log q(x|\theta)
\\end{equation}

Задачу максимизации можно переписать как задачу минимизации:

\\begin{equation}
    \\arg\\ \\max\_{\theta} \\mathbb E\_{x\sim p(x)} \\log q(x|\theta)=
    \\arg\\ \\min\_{\theta} \\left( -\\mathbb E\_{x\sim p(x)} \\log q(x|\theta) \\right)=
    \\arg\\ \\min\_{\theta} H(p, q)
\\end{equation}

Последняя величина называется кросс-энтропией распределений $p$ и $q$. Именно ее и принято оптимизировать для решения задач обучения с подкреплением (supervised learning).

Минимизацию на протяжении этого цикла статей мы будем проводить с помощью [Stochastic Gradient Descent (SGD)](https://en.wikipedia.org/wiki/Stochastic_gradient_descent), а точнее, его расширения на основе адаптивных моментов, пользуясь тем, что сумма градиентов по подвыборке (так называемому “минибатчу”) является несмещенной оценкой градиента минимизируемой функции.

## Классификация двух нормальных распределений логистической регрессией

Давайте попробуем решить ту же задачу, что была описана выше, методом максимального правдоподобия, взяв в качестве параметрического семейства $q(y|x, \theta)$ простейшую нейронную сеть. Получившаяся модель называется логистической регрессией. Полный код модели можно найти [тут](https://github.com/Monnoroch/generative/tree/master/logistic_regression), в статье же освещены только ключевые моменты.

Для начала нужно сгенерировать данные для обучения. Нужно сгенерировать минибатч меток классов и для каждой метки сгенерировать точку из соответствующей гауссианы:

```python
def input_batch(dataset_params, batch_size):
    input_mean = tf.constant(dataset_params.input_mean, dtype=tf.float32)
    input_stddev = tf.constant(dataset_params.input_stddev,dtype=tf.float32)
    count = len(dataset_params.input_mean)
    labels = tf.contrib.distributions.Categorical(probs=[1./count] * count)
        .sample(sample_shape=[batch_size])
    components = []
    for i in range(batch_size):
        components
            .append(tf.contrib.distributions.Normal(
                loc=input_mean[labels[i]],
                scale=input_stddev[labels[i]])
            .sample(sample_shape=[1]))
    samples = tf.concat(components, 0)
    return labels, samples

```

<br/>
Определим наш классификатор. Он будет простейшей нейронной сетью без скрытых слоев:

```python
def discriminator(input):
    output_size = 1
    param1 = tf.get_variable(
        "weights",
        initializer=tf.truncated_normal([output_size], stddev=0.1)
    )
    param2 = tf.get_variable(
        "biases",
        initializer=tf.constant(0.1, shape=[output_size])
    )
    return input * param1 + param2

```


И запишем функцию потерь &mdash; кросс-энтропию между распределениями реальных и предсказанных меток:

```python
labels, samples = input_batch(dataset_params, training_params.batch_size)
predicted_labels = discriminator(samples)
loss = tf.reduce_mean(tf.nn.sigmoid_cross_entropy_with_logits(
    labels=tf.cast(labels, tf.float32),
    logits=predicted_labels)
)
```

Ниже приведены графики обучения двух моделей: базовой и с L2-регуляризацией:

<center> {% include image_with_caption.html url="/images/posts/2017-10-31-generative-modeling-with-deep-learning/graph_3.png" description="Рис. 3. Кривая обучения логистической регрессии." width=520 %} </center>

Видно, что обе модели быстро сходятся к хорошему результату. Модель без регуляризации показывает себя лучше потому, что в этой задаче не нужна регуляризация, а она слегка замедляет скорость обучения. Давайте взглянем поближе на процесс обучения:

<center> {% include image_with_caption.html url="/images/posts/2017-10-31-generative-modeling-with-deep-learning/graph_4.gif" description="Рис. 4. Процесс обучения логистический регрессии." width=520 %} </center>

Видно, что обучаемая разделяющая поверхность постепенно сходится к аналитически вычисленной, при чем, чем она ближе, тем медленнее сходится из-за все более слабого градиента функции потерь.

## Регрессия

Задача регрессии &mdash; это задача предсказания одной случайной величины $\eta: \Omega \to Y$ на основе значений других случайных величин $\xi\_i: \Omega \to X\_i$. Например, предсказание роста человека по его полу (дискретная случайная величина) и возрасту (непрерывная случайная величина). Точно так же, как и в задаче классификации, нам дана размеченная выборка $D=\\{(x\_i,y\_i) \sim p(x, y), i=\\overline{1,N}\\}$. Предсказать значение случайной величины напрямую невозможно, ведь она случайная и, по сути, является функцией, поэтому формально задача записывается как предсказание ее условного ожидаемого значения:

\\[
    f(x)=\\mathbb E(\eta|\xi=x)= \\int\\limits\_Y y\\ p(y|x)\\mathrm{d}y
\\]

## Регрессия линейно зависимых величин с нормальным шумом

Давайте посмотрим, как решается задача регрессии на простом примере. Пусть есть две независимые случайные величины $\xi \sim Uniform(0, 10), \\varepsilon \sim N(0,1)$. Например, это высота дерева и нормальный случайный шум. Тогда мы можем предположить, что возраст дерева является случайной величиной $\eta=a \xi + b + \\varepsilon$. В таком случае по линейности математического ожидания и независимости $\xi$ и $\\varepsilon$:

\\[
    f(x)=E(\eta|\xi=x)=
    a\\ E(\xi|\xi=x)+b+E(\\varepsilon|\xi=x)=
\\]
\\[
    =a\\ E(\xi|\xi=x)+b+E(\\varepsilon)=
    a\\ x+b+0=a\\ x+b
\\]

<center> {% include image_with_caption.html url="/images/posts/2017-10-31-generative-modeling-with-deep-learning/graph_5.gif" description="Рис. 5. Линия регрессии задачи про линейно зависимые величины с шумом." width=520 %} </center>

## Решение задачи регрессии методом максимального правдоподобия

Давайте сформулируем задачу регрессии через метод максимального правдоподобия. Положим $q(y|x,\theta )=N(y; f(x; w), \sigma)$. Где $w$ &mdash; новый вектор параметров. Видно, что мы ищем  $f(x; w)$ &mdash; математическое ожидание $q(y|x,\theta)$, т.е. это корректно поставленная задача регрессии. Тогда

\\begin{equation}
    \\arg\\ \\min\_{\theta} H(p, q)=
    \\arg\\ \\min\_{\theta} \\left( -\\mathbb E\_{x\sim p(x)} \\log q(x|\theta) \\right)=
\\end{equation}
\\begin{equation}
    =\\arg\\ \\min\_{\theta} \\left( -\\mathbb E\_{x\sim p(x)} \\log N\\left(y;f\\left(x,w\\right),\sigma\\right) \\right)=
\\end{equation}
\\begin{equation}
    =\\arg\\ \\min\_{\theta} \\left( -\\mathbb E\_{(x,y) \sim p(x, y)}\\frac{\\left(f\\left(x; w\\right) - y\\right)\^2}{\\sigma\^2} \\right)
\\end{equation}

Состоятельной и несмещенной оценкой этого матожидания будет среднее по выборке

\\[
    \\arg\\ \\min\_{\theta}\\left( -\\sum\_{i=1..N}\\frac{\\left(f\\left(x\_i; w\\right) - y\_i\\right)\^2}{\sigma\^2} \\right)
\\]

Таким образом, для решения задачи регрессии удобно минимизировать среднеквадратичную ошибку на обучающей выборке.

## Регрессия величины линейной регрессией

Давайте попробуем решить ту же задачу, что была выше, методом из предыдущего раздела, взяв в качестве параметрического семейства $q(y|x, \theta)$ простейшую возможную нейронную сеть. Получившаяся модель называется линейной регрессией. Полный код модели можно найти [тут](https://github.com/Monnoroch/generative/tree/master/linear_regression), в статье же освещены только ключевые моменты.

Для начала нужно сгенерировать данные для обучения. Сначала мы генерируем минибатч входных переменных $\xi \sim Uniform(0, 10), \\varepsilon \sim N(0,1)$, после чего получаем сэмпл исходной переменной $\eta=a \xi + b + \\varepsilon$:

```python
def input_batch(dataset_params, batch_size):
    samples = tf.random_uniform([batch_size], 0., 10.)
    noise = tf.random_normal([batch_size], mean=0., stddev=1.)
    labels = (dataset_params.input_param1 * samples + dataset_params.input_param2 + noise)
    return labels, samples

```


<br/>
Определим нашу модель. Она будет простейшей нейронной сетью без скрытых слоев:

```python
def predicted_labels(input):
    output_size = 1
    param1 = tf.get_variable(
        "weights",
        initializer=tf.truncated_normal([output_size], stddev=0.1)
    )
    param2 = tf.get_variable(
        "biases",
        initializer=tf.constant(0.1, shape=[output_size])
    )
    return input * param1 + param2

```

<br/>
И запишем функцию потерь &mdash; L2-расстояние между распределениями реальных и предсказанных значений:

```python
labels, samples = input_batch(dataset_params, training_params.batch_size)
predicted_labels = discriminator(samples)
loss = tf.reduce_mean(tf.nn.sigmoid_cross_entropy_with_logits(
    labels=tf.cast(labels, tf.float32),
    logits=predicted_labels)
)

```

<br/>
Ниже приведены графики обучения двух моделей: базовой и с L2-регуляризацией:

<center> {% include image_with_caption.html url="/images/posts/2017-10-31-generative-modeling-with-deep-learning/graph_6.png" description="Рис. 6. Кривая обучения линейной регрессии." width=520 %} </center>

<center> {% include image_with_caption.html url="/images/posts/2017-10-31-generative-modeling-with-deep-learning/graph_7.png" description="Рис. 7. График изменения первого параметра с шагом обучения." width=520 %} </center>

<center> {% include image_with_caption.html url="/images/posts/2017-10-31-generative-modeling-with-deep-learning/graph_8.png" description="Рис. 8. График изменения второго параметра с шагом обучения." width=520 %} </center>

Видно, что обе модели быстро сходятся к хорошему результату. Модель без регуляризации показывает себя лучше потому, что в этой задаче не нужна регуляризация, а она слегка замедляет скорость обучения. Давайте взглянем поближе на процесс обучения:

<center> {% include image_with_caption.html url="/images/posts/2017-10-31-generative-modeling-with-deep-learning/graph_9.gif" description="Рис. 9. Процесс обучения линейной регрессии." width=520 %} </center>

Видно, что обучаемое математическое ожидание $\eta$ постепенно сходится к аналитически вычисленному, при чем, чем оно ближе, тем медленнее сходится из-за все более слабого градиента функции потерь.

## Другие задачи

В дополнение к изученным выше задачам классификации и регрессии есть и другие задачи так называемого обучения с учителем, в основном сводящиеся к отображению между точками и последовательностями: Object-to-Sequence, Sequence-to-Sequence, Sequence-to-Object. Так же есть и большой спектр классических задач обучения без учителя: кластеризация, заполнение пробелов в данных, и, наконец, явная или неявная аппроксимация распределений, которая и используется для генеративного моделирования. Именно о последнем классе задач будет идти речь в этом цикле статей.

## Генеративные модели

В следующей главе мы посмотрим, что такое генеративные модели и чем они принципиально отличаются от рассмотренных в этой главе дискриминативных. Мы посмотрим на простейшие примеры генеративных моделей и попробуем обучить модель, генерирующую сэмплы из простого распределения данных.

## Благодарности
Спасибо [Olga Talanova](https://www.linkedin.com/in/olga-talanova-b319b761/) за ревью текста.
Спасибо [Andrei Tarashkevich](https://github.com/andrewtar) за помощь с версткой этой статьи в Jekyll.
