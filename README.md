# Fruit_vendor Project

NAME:
-----
Fruit_vendor

AUTHOR:
-------
Andrew Lamson

FILES:
------
fruit_vendor_cli.py
fruit_vendor_parse.py
fruit_vendor_test.py
Data/fruit_data.json
Data/test_bad_format.json
Data/test_extended.json
Data/test_missing_field.json
Data/test_single_entry.json
requirements.txt

DESCRIPTION:
------------
fruit_vendor is a click-based CLI that returns cost information about 
trading fruits with different countries. The fruit, cost per ton, and 
trade volume in tons are specified by the user, while the country and 
commodity specific prices are provided by the third party JSON file. 

The fruit_vendor_cli.py file is the CLI and should be used to run the CLI.
The fruit_vendor_test.py file is the testing for the CLI and JSON parsing 
and can be run independently of the CLI.

DESIGN CONSIDERATIONS/ ASSUMPTIONS:
-----------------------------------
I designed this app with flexibility in mind first and foremost. This 
is a baseline proof of concept that will be extended further, 
therefore maximizing ease of design change and expandibility was my 
goal. For this reason I chose the click package to create the CLI 
interface as it makes changing the arguments, options, and formatting 
for the CLI interface much simpler than a traditional argparse CLI. In 
order to use that flexibility I changed the input from:
    python fruit_vendor_cli.py mango 53 405
    to:
    python fruit_vendor_cli.py cost mango 53 405
The addition of the cost allows for other commands to be added, such 
as a show action that might list all the valid commodities or 
countries. I actually built out a show command that is used referenced 
by the cost command if an improper commodity name is used.

The second focus was on testing. For a proof of concept it is 
important to proove that the initial design can handle what is being 
asked of it. I ran through several reasonable cases that the program 
might encounter with both JSON input and CLI input. The test cases are 
specific only towards pieces of code that I had written. For example, 
I did not test the built in type checking of click, but I did test if 
the input for a float arg was larger than zero when context demanded 
it be so. Though one note, the type click.STRING will end up passing 
ints and floats but my other error checking will handle that.

For the JSON file I assumed that since it is only updated once per day 
there is no immediate need to create a watcher. If any errors were to 
occur, due to the JSON file updating while the CLI was running, the 
run time of the CLI is small enough such that it could be repeated 
without issue. Additionally, I assumed that nightly meant the update 
occurred during non-peak hours for the US. Also, I only did minor
checking for the integrity of the JSON data. That could be expanded
well beyond the scope of this take-home and I assumed it best to
focus effort elsewhere.

I also assumed that for this setup, it did not make sense to create a 
redis databse to preprocess and store the data file. While it may make 
sense to expand in the future, currently the maximum number of entries 
being parsed in the JSON file is the number of fruits * number of 
countries. Assuming there are around 2000 types of fruits and every 
country produces all of them, that is under 400,000 entries.

Lastly, the output formatting for the cost was done using set space 
width for each value. I assumed that the total cost would be under a 
billion dollars and limited the number of spaces for that value to 9. 
The others are limited to 5 each. This however can be easily changed.


SETUP:
------

This guide assumes that the environment is installed with a recent 
version python3

1. Untar the fruit_vendor tar ball in the desired directory

    tar -xvf andrew_lamson_fruit_vendor.tar

2. Setup a virtual environment in the working directory for fruit_vendor

    python -m venv <fruit_vendor_working_dir>

3. Activate the virtual env

    source <fruit_vendor_working_dir>/bin/activate

4. Install the proper packages with requirements.txt

    pip install -r requirements.txt

USAGE:
------

1. Help

    python fruit_vendor_cli.py --help

    python fruit_vendor_cli.py <COMMAND> --help

2. Running the fruit_vendor example

    python fruit_vendor_cli.py cost mango 53 405

3. Running extended fruit_vendor example

    python fruit_vendor_cli.py cost mango 53 405
        --file_path=<fruit_vendor_working_dir>/Data/test_extended.txt

4. Running the show command

    python fruit_vendor_cli.py show commodity

5. Testing fruit_vendor

    python fruit_vendor_test.py


NOTES:
------

In order to run a cost command with a negative value in the click 
interface, the value "--" needs to be added after the before the 
argument in question but after any options as it will assume anything 
following that -- is an argument value.

For example:

python fruit_vendor_cli.py cost 
    --file_path=./Data/fruit_data.txt -- mango -5 50

There is a slight difference between the Docstrings of fruit_vendor_cli
and everything else. The cli requires more spacing since it is parsed
and sent to stdout for help. Whereas parse and test are not and have the
function above to give context for what each argument type should be.

The output for test has some stdout from the tests in it. I struggled to 
rectify it but was unable. Upon expansion that is something that I would
fix, but for the time being it is still readable and makes sense.


ADDITIONS:
----------

As part of expanding this project I have explored/planned for these items:

    - the watcher package to keep tabs on the json file as it changes. 
    - addition of a flat file as an alternative information source
    - updating the parsing moudule to handle both a flat file and json file
    - planned to add a redis database