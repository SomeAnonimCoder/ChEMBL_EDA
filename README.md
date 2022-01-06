# ChEMBL_EDA

EDA(разведывательный анализ даных) на базе данных chembl: анализ ингибиторов TDP1 и предсказание 
ингибирующей активности по дескрипторам.



<img src="https://upload.wikimedia.org/wikipedia/commons/a/a1/Protein_TDP1_PDB_1jy1.png" alt="drawing" width="200"/>
Ингибиторы TDP – новый класс противоопухолевых препаратов для решения проблемы резистентности опухолей к химиотерапии.
Механизм действия: ингибирование тирозил-ДНК-фосфодиэстеразы 1 (Tdp1) – одного из ключевых ферментов репарации ДНК. 
Tdp1 играет ключевую роль в удалении повреждений ДНК, индуцированных ингибиторами топоизомераз Top1, Top2 и другими 
противоопухолевыми препаратами. Ингибирование Tdp1 позволяет преодолеть резистентность опухоли к химиотерапии и 
усилить эффективность терапии.

# Получение данных

Данные были получены из [ChEMBL](https://www.ebi.ac.uk/chembl/), базы данных химических соединений и их 
биологической активности.

Она поддерживается Европейским институтом биоинформатики (EBI) и Европейской лабораторией молекулярной биологии 
(EMBL), базирующейся в Геномном кампусе Wellcome Trust, Хинкстон, Великобритания.

База данных, первоначально известная как StARlite, была разработана биотехнологической компанией Inpharmatica Ltd., 
позже приобретенной Galapagos NV. Данные были получены EMBL в 2008 году с наградой Wellcome Trust, что привело 
к созданию группы хемогеномики ChEMBL в EMBL-EBI под руководством Джона Оверингтона.

# Очистка и подготовка данных

В качестве фич были использованы [дескрипторы Липински](https://ru.wikipedia.org/wiki/%D0%9F%D1%80%D0%B0%D0%B2%D0%B8%D0%BB%D0%BE_%D0%9B%D0%B8%D0%BF%D0%B8%D0%BD%D1%81%D0%BA%D0%BE%D0%B3%D0%BE) и [EState-дескрипторы](https://sci-hub.yncjkj.com/10.1021/ci00001a012).

Правило Липински гласит, что в общем случае перорально активный препарат должен нарушать не более одного из 
следующих условий:

- Не более 5 донорных водородных связей (общее количество азот-водородных и кислород-водородных связей);
- Не более 10 акцепторных водородных связей (общее количество атомов азота или кислорода);
- Молекулярная масса соединения менее 500 a.e.м.;
- Коэффициент распределения октанол-вода не должен превышать 5.

Также вещества были разделены по активности в зависимости от IC50:
- IC50<1000nM -   активное
- IC50 > 10000nM - неактивное
- промежутоные значения, такие вещества были отброшены

# Exploratory data analisys

## Pairplot'ы для каждого из наборов дескрипторов:

![alt text](https://github.com/SomeAnonimCoder/ChEMBL_EDA/raw/master/imgs/PairplotLip.png)

![alt text](https://github.com/SomeAnonimCoder/ChEMBL_EDA/raw/master/imgs/PairplotStruct.png)

![alt text](https://github.com/SomeAnonimCoder/ChEMBL_EDA/raw/master/imgs/PairplotEState.png)

Все фичи независимы, кроме  за исключением MaxEI/MaxAEI.
## Boxplot'ы дескрипторов для активных и неактивных лигандов:

![alt text](https://github.com/SomeAnonimCoder/ChEMBL_EDA/raw/master/imgs/BoxplotLip.png)
![alt text](https://github.com/SomeAnonimCoder/ChEMBL_EDA/raw/master/imgs/BoxplotStruct.png)
![alt text](https://github.com/SomeAnonimCoder/ChEMBL_EDA/raw/master/imgs/BoxplotEState.png)

# Гипотеза:
Из рассмотренных выше дескрипторов все или значительная часть является статистически значимой для определения 
активности. Проверим эту гипотезу статистическим тестом, и затем попытаемся построить модель машинного обучения.

Эта гипотеза была проверена статистическим тетом Манна-Уитни, для большей части дескрипторов статистически значимая 
зависимость активности от них подтвердилась, для некоторых нет. Было отобрано 9 дескрипторов:
- MW - молярная масса
- LogP - коэффициент распределения октанол-вода
- NumDonors - число доноров водородной связи
- NumAcceptors - число акцепторов водородной связи
- NumSatHet - число насыщеных гетероциклов
- NumArHet - число ароматических гетероциклов
- NumRotBonds - число связей способных вращаться
- MaxAEI - максимум (по модулю) вектора дескрипторов EState
- MaxEI - максимум вектора дескрипторов EState
- MinEI - минимум вектора дескрипторов EState

Датафрейм с отфильтрованными дескрипторами был сохранен для дальнейшего использования.

# Машинное обучение

Были обучены следующие модели:
- SVM
- CART
- Random Forest
- AdaBoost
- CatBoost
- GradientBoost
- kNN

## Результаты обучения(для всех моделей применена кроссвалидация 10-fold):
![alt text](https://github.com/SomeAnonimCoder/ChEMBL_EDA/raw/master/imgs/Results.png)


## Важность фич с точки зрения разных алгоритмов:
![alt text](https://github.com/SomeAnonimCoder/ChEMBL_EDA/raw/master/imgs/CatBoost_feature_imp.png)
![alt text](https://github.com/SomeAnonimCoder/ChEMBL_EDA/raw/master/imgs/AdaBoost_feature_imp.png)
![alt text](https://github.com/SomeAnonimCoder/ChEMBL_EDA/raw/master/imgs/GradientBoost_feature_imp.png)
![alt text](https://github.com/SomeAnonimCoder/ChEMBL_EDA/raw/master/imgs/CART_feature_imp.png)
![alt text](https://github.com/SomeAnonimCoder/ChEMBL_EDA/raw/master/imgs/RandomForest_feature_imp.png)

Важность фич более-менее совпадает у разных алгоритмов.



# Выводы
Лучшей моделью по итогам тестов оказался CatBoost; AdaBoost, RandomForest, GradientBoost не сильно от него 
отстают. Остальные модели оказались существенно хуже по точности, F1, или им обоим сразу.

Сейчас все модели запущены с дефолтными параметрами. Возможно после оптимизации гиперпараметров часть моделей 
станет заметно лучше, надо будет проверить
