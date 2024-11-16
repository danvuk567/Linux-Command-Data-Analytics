# Linux Command Data Analytics

## Exploratory Data Analysis and Data Cleaning

Let's do some data cleaning and exploration on the data source text file in the steps outlined below.

1.	Let’s use the sed command to remove the carriage returns from the file and output to a new file that we can modify.

        input_file="Loan_prediction_mini_dataset.csv"
        output_file= “Loan_prediction_mini_dataset_cleaned.csv”

        sed 's/\r//' $input_file > $output_file

Let’s use the sed command and remove any leading or trailing whitespace from each line in the file.

        sed -i 's/^[ \t]*//;s/[ \t]*$//g' $output_file

Let’s use the sed command and remove the quotes around each field in the file.

        sed -i 's/"//g' $output_file

![clean_file.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/clean_file.jpg?raw=true)

2.	Let’s explore what the 1st 10 rows of data looks like using the cat and head commands on the file.

        cat $output_file | head

   To find the number of columns in the file, we can use head -n 1 to get the 1st row, use the tr command to replace commas by newline characters so that columns go to new lines and then use the wc -l command to count the number of lines for those columns. There are 11 columns.

        head -n 1 $output_file | tr ',' '\n' | wc -l

And we’ll use the wc -l command to count the number of lines to give us how many rows we have in the file. There are **8146** lines in the file.

        wc -l $output_file | head

![data_exploration_cleaning1.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/data_exploration_cleaning1.jpg?raw=true)

3.	Check for weird characters using grep -P with regular expression that matches any character not in the printable ASCII range including tab, carriage return, and newline. There are no characters that are not ASCII.

        grep -P '[^\x20-\x7E\t\r\n]' $output_file

 ![data_exploration_cleaning2.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/data_exploration_cleaning2.jpg?raw=true)

 4.	Let’s run an awk command on each column index using the comma delimiter and check if there are empty or ‘NULL’ or ‘#N/A’ values. We see that column 7 which represents “Rate” has missing or invalid values.
   

        awk -F ',' '$1 == "NULL" || $1 == "#N/A" || $1 == ""' $output_file
        awk -F ',' '$2 == "NULL" || $2 == "#N/A" || $2 == ""' $output_file
        awk -F ',' '$3 == "NULL" || $3 == "#N/A" || $3 == ""' $output_file
        awk -F ',' '$4 == "NULL" || $4 == "#N/A" || $4 == ""' $output_file
        awk -F ',' '$5 == "NULL" || $5 == "#N/A" || $5 == ""' $output_file
        awk -F ',' '$6 == "NULL" || $6 == "#N/A" || $6 == ""' $output_file
        awk -F ',' '$7 == "NULL" || $7 == "#N/A" || $7 == ""' $output_file

![data_exploration_cleaning3.jpg](https://github.com/danvuk567/Linux-Command-Data-Analytics/blob/main/images/data_exploration_cleaning3.jpg?raw=true)

We can use the awk command for column 7, specifically check if there are NULL, #N/A or empty values and count the number of lines with wc -l. There are **762** Rate values out of **8146** that are missing which should not pose a problem for any further analysis on the Rate column in Linux.

        awk -F ',' '$7 == "NULL"' $output_file | wc -l
        awk -F ',' '$7 == "#N/A"' $output_file | wc -l
        awk -F ',' '$7 == ""' $output_file | wc -l


