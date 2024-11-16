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


