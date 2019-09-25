`bash` cheat-sheet
=============

A summary of commands that I commonly use when writing bash scripts.  Sources are given where appropriate - please give these sources karma (upvotes) on Stackoverflow if you find the commands useful.  


**Contents**

* [Variables](#variables)
* [Repeating tasks](#repeating-tasks)
* [Control flow](#control-flow)
* [Searching for filenames](#searching-for-filenames)
* [Searching for text](#searching-for-text)
* [Organising files](#organising-files)
* [Interfacing with Python](#interfacing-with-python)
* [Interacting with processes](#interacting-with-processes)
* [Style guide](#style-guide)
* [awk examples](#awk-examples)

## Variables

**Strings, integers, floats**


Notes:
* bash variables are untyped [[0](http://tldp.org/LDP/abs/html/untyped.html)]
* don't use spaces around equals sign
* the dollar sign in front of variable name is used to reference the variable

```bash
var="Some text"
echo $var
> Some text

var2=10
echo $var2
> 10

e=2.718
echo $e
> 2.718
```

**Arrays**


Notes: 
* defined using circular brackets; space delimited
* index from 0
* return the whole array using: `${varlist[@]}`
* return the number of elements in an array using: `${#array[@]}`

```bash
declare -a varlist=(0.0 0.2 0.4 0.6 0.8 1.0 1.2 1.4 1.6 1.8 2.0)

echo ${varlist[0]} # first element
> 0.0
echo ${varlist[7]} # eighth element
> 1.4
echo ${#varlist[@]} # length of the array
> 11
echo ${varlist[@]} # print the whole array (used with loops; see below)
0.0 0.2 0.4 0.6 0.8 1.0 1.2 1.4 1.6 1.8 2.0
```


**Arithmetic** [
[1](https://www.tldp.org/LDP/abs/html/arithexp.html), [2](https://stackoverflow.com/questions/18093871/how-can-i-do-division-with-variables-in-a-linux-shell)
]

```bash
expr 15 + 222

expr 5 \* 7 # need to escape the multiplication character

expr $((7/2))

# Bash only does integer division, for division with floats, use `bc`
echo "7/2" | bc -l
```

Arithmetic with variable inputs
```bash
var1=15; var2=222
expr $var1 + $var2

var3=5; var4=7
expr $var3 \* $var4 # need to escape the multiplication character
```

Assigning output of expression using quotes
```bash
var1=15; var2=222
ans=`expr $var1 + $var2`
echo $ans
```

**String substitution**

* Strings can have substitution applied [[4](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_10_03.html)]
* Double forward slash (`//`) to replace all instances, single forward slash ('/') to replace just the first instance.  

```bash
string="I want to replace all spaces with underscores"
echo ${string// /_}
# or 
echo ${string//" "/"_"}
>> I_want_to_replace_all_spaces_with_underscores

# Using variables
filetype="csv"
filename="cleaned_data.txt"
echo ${filename/txt/$filetype}
>> cleaned_data.csv
```

**Concatenation**


[[6](https://stackoverflow.com/questions/4181703/how-to-concatenate-string-variables-in-bash)]

Combining strings

Concatenating to arrays



## Repeating tasks

1. Defined in a single line (using semicolons)
2. Defined also as a multi-line statement
3. Defined also using a C-style

```bash
# 1. Defined in a single line using semicolons
for i in 49 50 51; do echo $i; done

for i in AA "B" 73.2; do echo $i; done
```

```bash
# 2. Or spaced across multiple lines
for word in "Hello" "there" "computer"
do 
    echo $word
done
```

```bash
# 3. Or using a style similar to the C programming language
for ((i=1; i<10; i++)); do echo $i; done
```

Can also be defined using curly braces with commas between items (no spaces)
```bash
for i in {49,50,51}; do echo $i; done
```

**`for` loops** (of a sequence of numbers or letters)

* Curly braces can be used to define a sequence of numbers of letters

```bash
for index in {1..3}; do echo $index; done
for i in {1..3}; do for j in {a..c}; do echo $i$j; done; done
for i in {12..14}; do for j in {A..H}; do echo filename_${i}_${j}.csv; done; done

for index in {a..f}; do echo $index; done
for index in {G..Q}; do echo $index; done

# Using seq to loop through integers
start=10; stop=13
for index in $(seq $start $stop); do echo $index; done

# Using seq to loop through integers with a certain increment
start=10; stop=50; increment=4
for index in $(seq $start $increment $stop); do echo $index; done

# Using seq to loop through a regularly spaced grid of floats
for index in $(seq -w 0 .05 .3); do echo $index; done
```

**Loop over output from other `bash` commands**
```bash
for i in `ls`; do echo The following file/folder exists: $i; done

dirname="/bin"
for i in $(ls $dirname); do echo $i; done
```

**Iterate over an array**

By specifying the array within the loop definition (as above):

```bash
for g in "M" "F"
do
    echo $g
done
```

By defining an array and then iterating over the elements of that array: 
```bash
declare -a communities=(1 2 5 6 8 9 10 11)

for community in ${communities[@]}
do
    echo $community
done
```

Iterate over two arrays using a single index (such as when using an array job on a cluster).  

```bash
declare -a parameters1=(0.0 0.2 0.4 0.6 0.8 1.0 1.2 1.4 1.6 1.8 2.0)
declare -a parameters2=(1350 1400 1450 1500 1550 1600 1650 1700 1750 1800 1850)

# Use square brackets to find combined length
combinations=$[${#parameters1[@]} * ${#parameters2[@]}]

for ((c=0; c<$combinations; c++))
do
    echo "-----------------"
    echo "Combination number: $c"
    x=`expr $c % ${#parameters1[@]}`
    echo "Parameter 1: ${parameters1[$x]}"
    
    y=`expr $c / ${#parameters1[@]}`
    echo "Parameter 2: ${parameters2[$y]}"
done

# Alternatively (for 1 to a number) could use the following ... 
for c in $(seq 1 $combinations) 
do
    echo $c
done
```

* while loops
* do loops

## Control flow


* if statements
* elif statements
* else statements
* xargs

## Functions


**Functions in `bash`**

* Arguments are referenced in order (within the function)
* Arguments passed to the function without parenthese and a space between them

```bash
function copy_parameters {
    inputdir=$1
    outputdir=$2
    echo "Parameter 1: $inputdir"
    echo "Parameter 2: $outputdir"
}
copy_parameters "nine" 89
```


## Organising files

## Searching for filenames

### Searching for filenames within the current directory

List all `.csv` files within the current directory
```bash
ls *.csv
```

Recursively list all `.csv` files within the current directory and all subdirectories
```bash
ls *.csv
```


## Searching for text 

### Searching for text within a select set of files

Searching for the text "chips" within header (`.h`) and c (`.c`) files
```bash
grep chips *.[ch]
```

Include line number and highlight the search term
```bash
grep -n --color=auto chips *.[ch]
```

### Searching for text within the file system

Search current directory recursively (within all subdirs) for the term "package"
```bash
grep -r package .
```

Search current directory recursively for the term "library("
```bash
grep -r library\( .
```


**Organising files**


Delete all files in a folder *except* those of a particular type (`TypeA*.csv, TypeB*.csv`): 

```bash
find ./data/$s/RESULTS_COMMUNITY$i/Output/ \
    ! -name TypeA*.csv \
    ! -name TypeB*.dat \
    -maxdepth 1 \
    -type f \
    -delete
```

Find files with a unique stub/prefix before a particular (multi-character) delimiter

```bash
ls ./data/ | awk -F"CL" '{$0=$1}1' | uniq
>> Annual_outputs_
>> Calibration_output_
>> Cost_effectiveness_
>> Timestep_outputs_
>> Timestep_outputs_PConly_
```

Counting files
```bash
# Count number of files in a directory
ls log/ | wc -l
```


Find the number of lines of code in a file: 
```bash
wc -l src/constants.h 
```
 
Find the number of lines of code in a directory (recursively): 
```bash
# Count lines of R code recursively
find . -name '*.R' | xargs wc -l

# Count lines of C code (or code in header files) recursively
find src/*.[ch] | xargs wc -l

# Count lines of Python code recursively
find python -name '*.py' | xargs wc -l
```


Found the number of columns in a CSV file (assuming all columns are the same length): 
```bash
awk -F, '{print NF}' Output/Calibration_output_CL01_Za_B_V1.2_patch0_Rand10_PCseed0_0.csv | tail -n1
```


Find number lines in a list of files that match a pattern:
```bash
cat log/popart_ce_comm1.e* | wc -l
```

Find all non-empty files in the error output: 
```bash
find -L log/popart_ce_comm1.e* -maxdepth 1  -type f  ! -size 0
```



#### Git

Difference between two files in two different branches:
```bash
git diff mybranch master -- myfile.cs
```



#### Awk

Print the first and 5th line of a file, saving in another file called `output.log`
```bash
awk 'FNR==1 || FNR==5' test.txt > output.log
```

Pass bash variables to awk to specify the line to print in the above example
```bash
line_number=6
awk -v var="$line_number" 'FNR == 1 || FNR == line_number' test.txt > output.log

# Multiple variables, like this:
n1=6; n2=8
awk -v n1="$n1" -v n2="$n2" 'FNR == n1 || FNR == n2' test.txt > output.log
```



## Interfacing with Python


* Input arguments to Python.  

* Inputting a bash array into Python with `argparse`.  

## Interacting with processes

**Return process ID of just called command**

```bash
sleep 10 &
var=$!
echo $var
```

**Exit status**

* Return the exit status from a command using `$?`
* **Every** command has an exit status (so it's probably best practice to assign the exit code for the command of interest to a variable).  
* Check the `man` page of different commands to see what the different values of their exit status mean.  
* Generally speaking, an exit status of 0 means no errors occurred.  

```bash
echo "Hello"
exit_status=$?; echo $exit_status

grep spottieottiedopaliscious *.txt
echo $? # Probably 1 or 2 unless you've got such a file in the working directory!  
```


# Organising code

**Sourcing commands in a file**

* Use `source` to run commands that are stored in another file.  

```bash
echo "VAR=101" > params.sh
source params.sh
echo $VAR
>> 101
```


3) Find using file names for a subset of the characters in the file name.  

We output several files from the IBM and they've all got filenames of different length.  But they all have "CL" after the initial file name so we can split filenames on this delimiter and return the unique set of file names using bash (and awk).  I find this quite useful cause with a large number of runs the number of files can be very large.  

```bash
ls ./Output/ | awk -F"CL" '{$0=$1}1' | uniq
```


2) Deleting select file types.  

Within some of the results folders we output too much data (such as the debug scripts).  The find command can delete these files by only saving files which match a particular pattern:

For instance, only keep the files "TypeA*.csv" and "TypeB*.dat" (the exclamation mark is for **not** deleting those files):
```
find ./data/HIGH/RESULTS_COMMUNITY2/Output/ \
    ! -name TypeA*.csv \
    ! -name TypeB*.dat \
    -maxdepth 1 \
    -type f \
    -delete
```


1) Looping through pathnames to find subdirectory sizes:

I have the folder hierarchy ./data/HIGH/RESULTS_COMMUNITY5 etc where HIGH is the scenario name.  I didn't realise you could use curly brackets **twice** as a loop.  So to find the size of each community-specific subdirectory, the following works well:
```bash
du -hs data/{HIGH,LOW}/RESULTS_COMMUNITY{2,5,8,10,14,16,19}
> 912M data/HIGH/RESULTS_COMMUNITY2
> 1.1G data/HIGH/RESULTS_COMMUNITY5
> 1002M data/HIGH/RESULTS_COMMUNITY8
> 472M data/HIGH/RESULTS_COMMUNITY10
> 1.1G data/HIGH/RESULTS_COMMUNITY14
> 1.8G data/HIGH/RESULTS_COMMUNITY16
> 1.4M data/HIGH/RESULTS_COMMUNITY19
> 620M data/LOW/RESULTS_COMMUNITY2
> 1.4G data/LOW/RESULTS_COMMUNITY5
> 1.1G data/LOW/RESULTS_COMMUNITY8
> 703M data/LOW/RESULTS_COMMUNITY10
> 1.1G data/LOW/RESULTS_COMMUNITY14
> 1.4G data/LOW/RESULTS_COMMUNITY16
> 1.4M data/LOW/RESULTS_COMMUNITY19
```

## Interacting with processes

**Observing a process**


Notes:
* `watch` can be used to check a command every 2 seconds (by default)
* change refresh rate using `-n` option
* use ctrl-c to exit
* use single quotes to use `watch` with a command using a pipe

```bash
watch qstat

# Update every 5 seconds instead
watch -n5 qstatf

# Look at jobs associated with the user 'fraser' (refresh every 10 seconds)
watch -n10 'qsum | grep fraser'
```



## Style guide

* Continue command over a new line using `\` character


## awk examples

* Replace "A" with "1" in each line, skip lines that have an underscore in them:

```bash
echo ">NAME_123_CONSENSUS
GACTATACA
ATACTAGA
>NAME2_48_TEST
ATAGCGA" > test.txt

# Option 1
awk '!/_/{ gsub("A", "1") }1' test.txt

# Option 2
awk '{ for(j=1; j<=NF; j++) if ($j ~ "_") print; else { gsub("A","1",$0); print } }' test.txt

# Option 3
awk '{ if ($0 ~ "_") print; else {gsub("A", "1", $0); print} }' test.txt
```



