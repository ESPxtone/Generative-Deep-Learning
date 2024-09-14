# Generative-Deep-Learning

* mlp+cnn - распознование изображений с помощью многослойного перцептрона + сверточной нейронной сети.
* autoencoder - обучение автокодировщика на датасете fashion_mnist, размерность скрытого пространства = 2.
  Автокодировщик: кодировщик сжимает исходное изображение в пространство меньшей размерности, декодировщик восстанавливает сжатый вектор в исходное изображение.
  При выборе случайноого вектора из скрытого пространства происходит генерация новного изображения.
  Проблема автокодировщика в том, что скрытое пространство НЕ непрерывно, что приводит к не лучшей реконструкции ихображений, особенно при большой размерности скрытого пространства.
* vae_fashion - вариационный автокодировщик, датасет fashion_mnist. размерность скрытого простарнства 2.
  Отличие вариационного автокодировщика в том, что исходное изображение сжимается не в точку, а в многомерное нормальное распределение вокруг точки в скрытом пространстве.
  Отображение в нормальное распределение повышает непрерывность пространства и декодировщик декодирует соседине точки в более похожие изображения.
  Также в функцию потерь добавляется расстояние Кульбака-Лейблера(Kullback-Leibler divergence), которое штрафует модель если распределение отличается от нормального.
* vae_faces - вариационный автокодировщик, датасет celeba. размерность скрытого простарнства 200.
  Чем больше скрытое пространство тем больше параметров может выделить кодировщик.
  Также здесь показаны возможности преобразования изображений с помощью операций над векторами в скрытом пространстве.
* dcgan - Convolutional Generative Adversarial Network (DCGAN). Светрочная генеративно-состязательная модель: состоит из дискриминатора и генератора.
  Генератор создаёт изображение из случайного вектора (шума) также как декодер в автокодировщике, дискриминатор по сути обычный классификатор,
  который учится отличать сгенерированные и настоящие изображения. Оценки дискриминатора являются функцией потерь для генератора(бинарная кросс-энтропия), таким образом модели постоянно
  улучшают друг друга, поочередно обновляя веса. Проблема GAN в том, что одна из моделей может начать доминировть над другой, что приведёт к коллапсу функции потерь. Если начинает доминировать дискриминатор можно:
  увеличить dropout, уменьшить скорость обучения, уменьшить кол-во сверточных слоев, добавить шум в обучение дискриминатора. Если доминирует генератор - услиливаем дискриминатор, делая обратные шаги.
  Проблема в том, что нету прямой корреляции между потерями дискриминатора и генератора (это решается WGAN).
* wgan-gp - Wasserstein GAN  with Gradient Penalty. GAN с использованием функции потерь Вассерштайна вместо бинарной кросс-энтропии, она использует метки -1,1 вместо 1,0 и вместо сигмойды на выходите дискриминатора имеет значения в бесконечном диапазоне. В такой реализации дискриминатор обычно называют критиком (потому что она даёт не вероятность, а оценку). Критик WGAN пытается максимизировать разницу между оценками реальных и сгенерированных изображений, при этом реальные изображения получают оценки выше. Генератор WGAN пытается создать изображения, которые будут получать высокие оценки критика. Так как критик выдаёт оценки в бесконечном диапазоне стоит задать ему определенные ограничения (Ограничение Липшица) - критик должен быть одномерной липщицевой непрерывной функией, по сути , мы ограничиваем скорость с которой могут меняться оценки для двух разных изображений (градиент <= 1). Можно ввести ограничения на веса критика, но это плохой вариант, вместо этого в функцию потерь добавляется штраф за градиент - измеряется квадрат разности между нормой градиента прогнозов для входных изображений и 1. Так как градиент сложно вычислить повсюду вместо этого мы его вычисляем только в некоторых точках (интерполированные изображения). Все эти дополнения повышают стабильность обучения GAN и упрощают оценки функции потерь (она все время сходится). Также важно НЕ использовать пакетную нормализацию в WGAN (она снижает эффективность штрафа за градиент).
