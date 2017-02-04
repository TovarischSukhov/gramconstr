#Конструкция "уж Х -то ..."
##Данные
НКРЯ - 111 вхождений;   
RuTenTen - 350 вхождений

###Гипотеза:
Данная кострукция тяготеет к глаголам со значением "знать".
X тяготеет к местоимениям.

###Разметка:
На полученных данных были выделены следующие признаки:

1. X
  2. N\P
  3. S_O
  4. IsPerson
5. VERB
  6. Time
  7. KNOW
  8. IsAct
 
##Статистика

* Тяготние к "знать"

    table(new_data$data.KNOW, new_data$data.N.P)
    chisq.test(table(new_data$data.KNOW, new_data$data.N.P))
  
 Получившаяся таблица для подсчетов:
 
  0 | N  | P
----|----|----
  0 | 62 | 175
  1 | 14 | 200

         Pearson's Chi-squared test with Yates' continuity correction
      data:  table(new_data$data.KNOW, new_data$data.N.P)
      X-squared = 29.506, df = 1, p-value = 5.576e-08
Получившееся значение p-value<0.001, что позволяет сделать вывод, что корреляция есть.

* Модели

Также, были обучены и протестированы несколько моделей randomForest, целью которых было предсказать, будет ли у глагола значение знать. 
Успешнее всего себя покзазала данная модель:

        randomForest(formula = data.KNOW ~ data.N.P + data.S_O + data.Perso., data = train_set)
        predictions <- predict(output.forest, newdata = test_set)

        test_answ  <- test_set$data.KNOW
        test_answ[is.na(test_answ)] <- 0

        table(predictions,test_set$data.KNOW)
        
Результат:

    OOB estimate of  error rate: 22.5%
      
Процент ошибок на тестовой выборке: 18,5%

Таблица ошибок:

 . |  0  | 1
---|-----|---
 0 | 115 | 40
 1 |  7  | 91

