# Изучение влияние параметра “темп обучения” на процесс обучения нейронной сети на примере решения задачи классификации Oregon Wildlife с использованием техники
## 1.С использованием  техники обучения Transfer Learning обучить нейронную сеть EfficientNet-B0 (предварительно обученную на базе изображений imagenet) для решения задачи классификации изображений Oregon WildLife с использованием фиксированных темпов обучения 0.1, 0.01, 0.001, 0.0001
Файл train_transfer.py

![leg_2](https://user-images.githubusercontent.com/80068414/111864984-537fde80-8975-11eb-9733-20c220b1e233.png)

Метрика качества на валидации

![acc_1](https://github.com/EugenTrifonov/lab_3/blob/main/graph/epoch_categorical_accuracy.svg)

Функция потерь на валидации

![loss_1](https://github.com/EugenTrifonov/lab_3/blob/main/graph/epoch_loss.svg)

По графикам метрики качества и функции потерь можно определить, что наилучшее качество достигается при темпе обучения 0.001. Выберем темп обучения равный 0.001 как оптимальный.
## 2.Реализовать и применить в обучении политики изменения темпа обучения, а также определить оптимальные параметры для каждой политики
## A)Пошаговое затухание (Step Decay)
Файл train_step_decay.py
```python
def step_decay(epoch):
   initial_lrate = 0.1
   drop = 0.4
   epochs_drop = 3.0
   lrate = initial_lrate * math.pow(drop,  
           math.floor((1+epoch)/epochs_drop))
   return lrate
```
Были реализованы 3 варианта параметров для пошагового затухания:
 
 1)initial_lrate = 0.1
   drop = 0.3
   epochs_drop = 5.0 (синий график)
   
 2)initial_lrate = 0.1
   drop = 0.5
   epochs_drop = 10.0 (красный график)
   
 3)initial_lrate = 0.1
   drop = 0.4
   epochs_drop = 3.0 (голубой график)
   
![leg_1](https://user-images.githubusercontent.com/80068414/111865770-02beb480-897a-11eb-8821-2c1f778f9fff.png)

Метрика качества на валидации

![acc_2](https://github.com/EugenTrifonov/lab_3/blob/main/graph/epoch_categorical_accuracy_step.svg)

Функция потерь на валидации

![loss_2](https://github.com/EugenTrifonov/lab_3/blob/main/graph/epoch_loss_step.svg)

Наиболее оптимальным оказался случай с параметрами initial_lrate = 0.1 drop = 0.4 epochs_drop = 3.0 (уменьшение на 60% каждые 3 эпохи, начиная с 0.1). При таких параметрах алгоритм сошёлся к 24-й эпохе, точность  равна 0.8826. По сравнению с оптимальным фиксированным темпом обучения(0.001) точность увеличилась на 0.28%.
  
## B)Экспоненциальное затухание (Exponential Decay)
Файл train_exp_decay.py
```python
def exp_decay(epoch):
   initial_lrate = 0.1
   k = 0.5
   lrate = initial_lrate * exp(-k*epoch)
   return lrate
lrate = LearningRateScheduler(exp_decay)
```

Были реализованы 4 варианта параметра k для экспоненциального затухания(начальный темп обучения во всех случаях равен 0.1) :

1)k=0.1

2)k=0.3

3)k=0.4

4)k=0.5

![leg_1](https://user-images.githubusercontent.com/80068414/111866408-461b2200-897e-11eb-9bed-b15c15ccd9a6.png)

Метрика качества на валидации

![acc_3](https://github.com/EugenTrifonov/lab_3/blob/main/graph/epoch_categorical_accuracy_exp.svg)

Функция потерь на валидации 

![loss_3](https://github.com/EugenTrifonov/lab_3/blob/main/graph/epoch_loss_exp.svg)

Наиболее оптимальным оказался параметр k=0.5 при initial_lrate=0.1. При таком параметре алгоритм сошёлся к 16-й эпохе, точность равна 0.8868. По сравнению с оптимальным фиксированным темпом обучения(0.001) точность увеличилась на 0.68%.
## 3.Анализ результатов
По результатам двух вариантов реализации изменения темпа обучения можно сделать вывод, что наилучшим является случай экспоненциального затухания с оптимальными параметрами k=0.5 и initial_lrate=0.1. Это обеспечило увеличение точности на 0.68%, при этом увеличилась скорость сходимости(алгоритм сошёлся к 16-й эпохе). По сравнению с пошаговым затуханием, прирост точности экспоненциального больше на 0.40%, а алгоритм сошёлся на 8 эпох раньше. 
