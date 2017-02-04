

```R
data <- read.table('data - stable.tsv', header=TRUE, sep ='\t', encoding="UTF-8", quote = '')
```


```R
data_clean <- data.frame(data$X.1, data$X, data$VERB, data$N.P, data$S_O, data$Time, data$KNOW, data$Perso., data$Act)
data_clean[is.na(data_clean)] <- 0
data_clean[,7] <- factor(data_clean[,7])

train_set <- data_clean[1:200,]
test_set <- data_clean[201:453,]
```


```R
new_data  <- data_clean[data_clean$data.NP != '-',]
for(i in c(1:length(data_clean[,1]))){
    if(data_clean[i,4]!='-'){
        new_data[i,]  <- data_clean[i,]
    }
}
new_data$data.N.P <- droplevels(new_data$data.N.P)
table(new_data$data.KNOW, new_data$data.N.P)
fisher.test(table(new_data$data.KNOW, new_data$data.N.P))
```


       
          N   P
      0  62 175
      1  14 200



    
    	Fisher's Exact Test for Count Data
    
    data:  table(new_data$data.KNOW, new_data$data.N.P)
    p-value = 1.398e-08
    alternative hypothesis: true odds ratio is not equal to 1
    95 percent confidence interval:
      2.678175 10.112650
    sample estimates:
    odds ratio 
      5.044388 
    



```R
library(randomForest)
output.forest <- randomForest(formula = data.KNOW ~ data.N.P + data.S_O + data.Perso., data = train_set)

# View the forest results.
print(output.forest) 


predictions <- predict(output.forest, newdata = test_set)

test_answ  <- test_set$data.KNOW
test_answ[is.na(test_answ)] <- 0

incorrect  <- 0
for(i in c(1:length(test_answ))){

        if(predictions[i] != test_set$data.KNOW[i]){
            incorrect <- incorrect + 1
        
    }
}
(incorrect)/length(test_answ)

table(predictions,test_set$data.KNOW)
```

    
    Call:
     randomForest(formula = data.KNOW ~ data.N.P + data.S_O + data.Perso.,      data = train_set) 
                   Type of random forest: classification
                         Number of trees: 500
    No. of variables tried at each split: 1
    
            OOB estimate of  error rate: 22.5%
    Confusion matrix:
        0  1 class.error
    0 106 11  0.09401709
    1  34 49  0.40963855
    


0.185770750988142



               
    predictions   0   1
              0 115  40
              1   7  91



```R
model <- glm(data.KNOW~ data.N.P + data.S_O + data.Perso., data = train_set, family = "binomial")
model
#predict.glm(model, test_set, type='response')
```


```R
model <- glm(data.KNOW~ data.N.P + data.S_O, data = train_set, family = "binomial")
model
#predict.glm(model, test_set, type='response')
predictions <- predict(model, newdata = test_set)

test_answ  <- test_set$data.KNOW
test_answ[is.na(test_answ)] <- 0


incorrect  <- 0
for(i in c(1:length(test_answ))){

        if(predictions[i] != test_set$data.KNOW[i]){
            incorrect <- incorrect + 1
        
    }
}
(incorrect)/length(test_answ)
table(predictions,test_set$KNOW)
```
