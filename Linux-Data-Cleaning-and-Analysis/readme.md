# Linux Command Data Analytics

## Data Cleaning

Let's do some data cleaning and exploration using Linux bash commands on the data source text file in the steps outlined below. I used an Oracle cloud virtual terminal running a Linux Shell for this excercise.

1. Let’s use the **sed** command to remove the carriage returns from the file and output to a new file that we can modify.

        input_file="Loan_prediction_mini_dataset.csv"
        output_file= “Loan_prediction_mini_dataset_cleaned.csv”

        sed 's/\r//' $input_file > $output_file

Let’s also use the sed command and remove any leading or trailing whitespace from each line in the file.

        sed -i 's/^[ \t]*//;s/[ \t]*$//g' $output_file

Let’s use the sed command again and remove the quotes around each field in the file.

        sed -i 's/"//g' $output_file

![clean_file.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/clean_file.jpg?raw=true)

2. Let’s explore what the 1st 10 rows of data looks like using the **cat** and **head** commands on the file.

        cat $output_file | head

   To find the number of columns in the file, we can use head -n 1 to get the 1st row, use the tr command to replace commas by newline characters so that columns go to new lines and then use the **wc -l** command to count the number of lines for those columns. There are **11 columns**.

        head -n 1 $output_file | tr ',' '\n' | wc -l

And we’ll use the wc -l command to count the number of lines to give us how many rows we have in the file. There are **8146 rows** in the file.

        wc -l $output_file | head

![data_exploration_cleaning1.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/data_exploration_cleaning1.jpg?raw=true)

3. Check for weird characters using **grep -P** with regular expression that matches any character not in the printable ASCII range including tab, carriage return, and newline. There are no characters that are not ASCII.

        grep -P '[^\x20-\x7E\t\r\n]' $output_file

 ![data_exploration_cleaning2.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/data_exploration_cleaning2.jpg?raw=true)

4. Let’s run an **awk** command on each column index using the comma delimiter and check if there are empty or 'NULL' or '#N/A' values. We see that column 7 which represents *Rate* has missing or invalid values.
   

        awk -F ',' '$1 == "NULL" || $1 == "#N/A" || $1 == ""' $output_file
        awk -F ',' '$2 == "NULL" || $2 == "#N/A" || $2 == ""' $output_file
        awk -F ',' '$3 == "NULL" || $3 == "#N/A" || $3 == ""' $output_file
        awk -F ',' '$4 == "NULL" || $4 == "#N/A" || $4 == ""' $output_file
        awk -F ',' '$5 == "NULL" || $5 == "#N/A" || $5 == ""' $output_file
        awk -F ',' '$6 == "NULL" || $6 == "#N/A" || $6 == ""' $output_file
        awk -F ',' '$7 == "NULL" || $7 == "#N/A" || $7 == ""' $output_file

![data_exploration_cleaning3.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/data_exploration_cleaning3.jpg?raw=true)

We can use the awk command again for column 7, specifically check if there are 'NULL', '#N/A' or empty values, and count the number of lines with wc -l. There are **762** Rate values out of **8146** that are missing which should not pose a problem for any further analysis on the Rate column in Linux.

        awk -F ',' '$7 == "NULL"' $output_file | wc -l
        awk -F ',' '$7 == "#N/A"' $output_file | wc -l
        awk -F ',' '$7 == ""' $output_file | wc -l

![data_exploration_cleaning4.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/data_exploration_cleaning4.jpg?raw=true)

Columns 8 to 11 appear to have no missing or invalid values.

        awk -F ',' '$8 == "NULL" || $8 == "#N/A" || $8 == ""' $output_file
        awk -F ',' '$9 == "NULL" || $9 == "#N/A" || $9 == ""' $output_file
        awk -F ',' '$10 == "NULL" || $10 == "#N/A" || $10 == ""' $output_file
        awk -F ',' '$11 == "NULL" || $11 == "#N/A" || $11 == ""' $output_file

![data_exploration_cleaning5.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/data_exploration_cleaning5.jpg?raw=true)

5. Check to make sure each column has the expected data type using the awk command with comma delimiter and row > 1 to ignore the 1st header row.

   The 1st, 2nd, 3rd columns should contain a number. No rows show non-number.

        awk -F ',' 'NR > 1 && ($1 !~ /^[0-9]+$/ || $2 !~ /^[0-9]+$/ || $3 !~ /^[0-9]+$/) {print $0}' $output_file | wc -l
   
   The 4th and 5th columns are string. No rows show non-string.

        awk -F ',' 'NR > 1 && ($4 !~ /^[A-Za-z ]+$/ || $5 !~ /^[A-Za-z ]+$/) {print $0}' $output_file | wc -l

   The 7th column is decimal, so we check for numbers or decimals. We expect **762** that are not numbers or decimals because they are empty values.

        awk -F ',' 'NR > 1 && $7 !~ /^[0-9]+(\.[0-9]+)?$/ {print $0}' $output_file | wc -l

   The 8th column is number, so we check for numbers.

        awk -F ',' 'NR > 1 && ($8 !~ /^[0-9]+$/) {print $0}' $output_file | wc -l

   The 9th column is decimal, so we check for numbers or decimals.

        awk -F ',' 'NR > 1 && $9 !~ /^[0-9]+(\.[0-9]+)?$/ {print $0}' $output_file | wc -l
   
   The 10th column is string, so we check for numbers or decimals.

        awk -F ',' 'NR > 1 && ($10 !~ /^[A-Za-z ]+$/) {print $0}' $output_file | wc -l

   The 11th column is number, so we check for numbers.

        awk -F ',' 'NR > 1 && ($11 !~ /^[0-9]+$/) {print $0}' $output_file | wc -l

![data_exploration_cleaning6.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/data_exploration_cleaning6.jpg?raw=true)   

6. Let’s check if there are duplicates in the file using the **sort** and **uniq -d** command. There are none.

        sort $output_file | uniq -d

![data_exploration_cleaning7.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/data_exploration_cleaning7.jpg?raw=true)


## Data Analysis

In this section, we will ask some questions and do some analysis using Linux bash commands.

### **What is the income of the loan applicant with ID #18983?**

1. Let’s show the applicant ID which is the 1st column of the file using the **cut** command with comma delimiter and first column and use the head command to show the 1st 10 rows.

        cut -d ',' -f 1 $output_file  | head

![income_applicant_ID1.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/income_applicant_ID1.jpg?raw=true)

2. Let’s use the cat command on the file and then get the row that has the value 18983 in any column using grep -n. We get one row as the 4754th row where 189823 only appears in the ID column.

        cat  $output_file | grep -n 18983   
   
![income_applicant_ID2.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/income_applicant_ID2.jpg?raw=true)

3. Let’s combine the cut and grep command from #1 and #2 to extract row number and value of applicant **Id** column containing 18983. We’ll use another cut command to with colon delimiter and first column to get the line value.

        cut -d ',' -f 1 $output_file | grep -n 18983 | cut -d ':' -f 1

![income_applicant_ID3.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/income_applicant_ID3.jpg?raw=true)

4. In this last step, we’ll store the command in #3 in the variable line_num. We’ll then use the sed -n command to print the line stored in the line_num variable and apply the cut command with comma delimiter and 3rd column. We’ll store that in the income variable and then print the value which is **$120,000**.

![income_applicant_ID4.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/income_applicant_ID4.jpg?raw=true)   


### **Which applicant ID had the highest loan rate?**

Let’s break this down into steps using the awk command.

1. The *Rate* column is the 7th column. Let’s test an awk command separating the fields by ‘,’ and row number > 1 to ignore the header row and show the 1st 10 rows of the Rate column.

        awk -F ',' 'NR > 1 {print $7}' $output_file | head

![highest_loan_rate1.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/highest_loan_rate1.jpg?raw=true)  

2. We can use max variable to loop through the 7th column and track the last maximum value of Rate and show that the maximum Rate is **21.74%** and the applicant ID is **15744**.

        awk -F ',' 'NR > 1 {if ($7 > max) {max = $7; id = $1}} END {print "ID:", id, "Max Loan Rate:", max "%"}' $output_file

![highest_loan_rate2.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/highest_loan_rate2.jpg?raw=true)  


### **How many applicants that owned a home or had a mortgage were borrowing for home improvement?**

1. The “Home” column is the 4th column. Let’s test an awk command separating the fields by ‘,’ and row number > 1 to ignore the header row and show the 1st 10 rows of applicant ID with **Home** = "OWN" or "MORTGAGE".

        awk -F ',' 'NR > 1 && ($4 == "OWN" || $4 == "MORTGAGE")  {print $1}' $output_file | head

![count_applicants_homeimprovement1.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/count_applicants_homeimprovement1.jpg?raw=true)

2. The *Intent* column is the 5th column. Let’s test an awk command separating the fields by ‘,’ and row number > 1 to ignore the header row and show the 1st 10 rows of applicant ID with Intent = "HOMEIMPROVEMENT".

        awk -F ',' 'NR > 1 && $5 = " HOMEIMPROVEMENT " {print $1}' $output_file | head
   
![count_applicants_homeimprovement2.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/count_applicants_homeimprovement2.jpg?raw=true)

3. Let’s combine the 2 awk commands to show the 1st 10 rows of applicant ID column filtered out by Home = "OWN" or "MORTGAGE" and Intent = "HOMEIMPROVEMENT".

        awk -F ',' 'NR > 1 && ($4 == "OWN" || $4 == "MORTGAGE") && $5 == "HOMEIMPROVEMENT" {print $1}' $output_file | head 
   
![count_applicants_homeimprovement3.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/count_applicants_homeimprovement3.jpg?raw=true)


4. The final step is to combine the filter conditions from #1 and #2 and use a count variable to count rows for filtered data. We print the number of applicants if rows are returned with count > 0. The number of applicants that owned a home or had a mortgage and were borrowing for home improvement is **539**.

        awk -F ',' 'NR > 1 && ($4 == "OWN" || $4 == "MORTGAGE") && $5 == "HOMEIMPROVEMENT" {count++} END {if (count > 0) print "No. of applicants:", count; else print "None"}' $output_file

![count_applicants_homeimprovement4.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/count_applicants_homeimprovement4.jpg?raw=true)


### **What is the average age of those borrowing more than 20% of their income and making less than $15,000?**

We'll break this down into steps using the awk command.

1. The borrowing % of income is in the *Percent_income* column which is the 9th column. Let’s test an awk command separating the fields by ‘,’ and row number > 1 to ignore the header row and show the 1st 10 rows of Percent_income > 0.2.

        awk -F ',' 'NR > 1 && $9 > 0.2 {print $9}' $output_file | head

![average_age_borrowing_percent_income1.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/average_age_borrowing_percent_income1.jpg?raw=true)

2. The *Income* column is the 3rd column. Let’s test an awk command separating the fields by ‘,’ and row number > 1 to ignore the header row and show the 1st 10 rows of Income < 15000.

        awk -F ',' 'NR > 1 && $3 < 15000 {print $3}' $output_file | head

![average_age_borrowing_percent_income2.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/average_age_borrowing_percent_income2.jpg?raw=true)

3. The *Age* column is the 2nd column. Let’s combine the 2 awk commands to show the 1st 10 rows of age column filtered out by Percent_income > 0.2 and Income < 15000.

        awk -F ',' 'NR > 1 && $9 > 0.2 && $3 < 15000 {print $2}' $output_file | head

![average_age_borrowing_percent_income3.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/average_age_borrowing_percent_income3.jpg?raw=true)

4. The final step is to combine the filter conditions from #1 and #2, use a count variable to count rows for filtered data, and aggregate the age column values we explored in #3 using the sum variable. If rows are returned with count > 0, the average is calculated as sum/count and we print the result by rounding to 0 using formatting of %.0f\n. The average age of those borrowing more than 20% of their income and making less than $15,000 is **26**.
    
        awk -F ',' 'NR > 1 && $9 > 0.2 && $3 < 15000 {count++; sum+=$2} END {if (count > 0) printf "Average age: %.0f\n", sum/count; else print "No matching records"}' $output_file

![average_age_borrowing_percent_income4.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/average_age_borrowing_percent_income4.jpg?raw=true)


### **Which applicants are seeking loan approval status, have defaulted on their loan previously, looking to do debt consolidation, and borrowing more than 50% of their income?**

Let’s break this down into steps using the awk command.

1. The *Default* column is the 10rd column. Let’s test an awk command separating the fields by ‘,’ and row number > 1 to ignore the header row and show the 1st 10 rows of applicant ID with Default = 'Y'.

        awk -F ',' 'NR > 1 && $10 == "Y" {print $1}' $output_file | head

![applicants_default_borrowing_percent_income_debt_cons1.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/applicants_default_borrowing_percent_income_debt_cons1.jpg?raw=true)

2. The *Intent* column is the 5th column. Let’s test an awk command separating the fields by ‘,’ and row number > 1 to ignore the header row and show the 1st 10 rows of applicant ID with Intent = 'DEBTCONSOLIDATION'.

        awk -F ',' 'NR > 1 && $5 = "DEBTCONSOLIDATION" {print $1}' $output_file | head

![applicants_default_borrowing_percent_income_debt_cons2.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/applicants_default_borrowing_percent_income_debt_cons2.jpg?raw=true)

3. The borrowing % of income is in the Percent_income column which is the 9th column. Let’s test an awk command separating the fields by ‘,’ and row number > 1 to ignore the header row and show the 1st 10 rows of applicant ID with Percent_income > 0.5.

        awk -F ',' 'NR > 1 && $9 > 0.5 {print $1}' $output_file | head
   
![applicants_default_borrowing_percent_income_debt_cons3.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/applicants_default_borrowing_percent_income_debt_cons3.jpg?raw=true)

4. The final step is to combine the filter conditions from #1, #2, and #3 and show the 1st 10 rows of ID column filtered out by Default = 'Y', Intent = 'DEBTCONSOLIDATION', and Percent_income > 0.5. We have **3** applicants that are seeking loan approval status, have defaulted on their loan previously, looking to do debt consolidation, and borrowing more than 50% of their income: **18204 18327 28787**.

        awk -F ',' 'NR > 1 && $10 == "Y" && $5 == "DEBTCONSOLIDATION" && $9 > 0.5 {print $1}' $output_file | head

![applicants_default_borrowing_percent_income_debt_cons4.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/applicants_default_borrowing_percent_income_debt_cons4.jpg?raw=true)

