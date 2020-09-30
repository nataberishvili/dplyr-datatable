# dplyr-datatable
fast transition between dplyr and data.table R

In this post I compare the syntax of R's two most powerful data manipulation libraries: dplyr and data.table. I am not here to advocate one package or the other. However, while working on a project with unusually large datasets, my preferred package became data.table, for speed and memory efficiency. I took a legacy project in dplyr and recreated it in data.table. I will show the comparison of syntax and how to translate from dplyr to data.table or vice versa. 



I will use R's built-in AutoClaims dataset of automobile insurance claims. Let's install and load the libraries and prepare the dataset for the exercise.

#install.packages("dplyr")  
#install.packages("data.table")  
#install.packages("insuranceData")  
library(dplyr)  
library(data.table)  
library(insuranceData)  
data("AutoClaims") #load the dataset  
data <- AutoClaims #rename the dataset  
data <- data.table(data) #convert it to data.table  

Data has 6773 rows and 5 columns. Let's see how the data looks.  

> head(data)  
STATE CLASS GENDER AGE PAID  
1 STATE 14 C6 M 97 1134.44  
2 STATE 15 C6 M 96 3761.24  
3 STATE 15 C11 M 95 7842.31  
4 STATE 15 F6 F 95 2384.67  
5 STATE 15 F6 M 95 650.00  
6 STATE 15 F6 M 95 391.12  


TOP 10 most frequently used data manipulation functions  


1. Filter by rows
Let's filter for men aged 79 or younger.
data[AGE <= 79 & GENDER == "M"] #datatable  
data %>% filter(AGE <= 79 & GENDER == "M") #dplyr  

2. Select by columns  
Let's select the columns GENDER, AGE, and PAID.
data[, .(GENDER, AGE, PAID)] #datatable  
data %>% select(GENDER, AGE, PAID) #dplyr  

3. Add New Columns  
Let's convert PAID from Dollars to Euros using a 0.85 Dollar/Euro conversion rate. The resulting new variable is added to our dataset.  
data[, PAID.IN.EURO := PAID * 0.85] #datatable          
data %>% mutate(PAID.IN.EURO = PAID * 0.85) #dplyr

4. Delete Column
let's delete the newly created variable  
data[, !c("PAID.IN.EURO")] #datatable    
data[, PAID.IN.EURO:= NULL] #datatable (alternative way)        
select(data, -PAID.IN.EURO) #dplyr  

5. Create New Column  
let's create a new variable and drop existing ones. As a result dataset will have only new variable  
data[, .(PAID.IN.EURO = PAID * 0.85)] #datatable  
data %>% transmute(PAID.IN.EURO = PAID * 0.85) #dplyr  

6. Summarize by Column  
What is the average insurance claim paid?  
data[, mean(PAID)] #datatable  
data %>% summarise(AVG.PAID = mean(PAID)) #dplyr  

7. Rename variable  
With data.table we use the setnames function, which references the old names and then new names. With dplyr, the order is reversed.
setnames(data, c("GENDER", "CLASS"), c("gender", "class"))  
data %>% rename("gender" = "GENDER", "class" = "CLASS") #dplyr  

8. Sort data in ascending or descending order  
setorder(data, PAID) #datatable ascending order  
setorder(data, -PAID) #datatable descending order  
data %>% arrange(PAID) #dplyr ascending   
data %>% arrange(desc(PAID)) #dplyr descending  

9. Group by
10. Counting Observations  
Let's count how many observations we have for each class by chaining functions in dplyr  
data[, .N, by= "CLASS"] #datatable  
data %>% #dplyr  
 group_by(CLASS) %>%  
 summarise(n())  
Now let's see more examples of how to chain functions within dplyr and data.table.  


Chaining Functions  
In dplyr, we chain functions using the %>% pipe operator. In data.table, several functions can be written concisely in one line of code, or by using square brackets for more complicated chaining.  



Conclusion  

 Data.table uses shorter syntax than dplyr but is often more nuanced and complex. dplyr use a pipe operator, which is more intuitive for beginners to read and debug. Moreover, many other libraries use pipe operators, such as ggplot2 and tidyr. While data.table and dplyr are both widely used in the R community, dplyr is used more broadly and thus offers more opportunities for collaboration.   
Are memory and speed important? when building complex reports and dashboards, and especially when working with very large datasets, data.table has distinct advantages.
It is up to you which library to use for your analysis!
