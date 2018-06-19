bash cheatsheet
=============

Searching for "chips" within header (`.h`) and c (`.c`) files
```bash
grep chips *.[ch]
```

Search current directory recursively for the term "package"
```bash
grep -r package .
```

Search current directory recursively for the term "library("
```bash
grep -r library\( .
```



Making new variables
    expr
    using {} around variables
    arrays: declare -a array


#### `For` loops (of numbers or letters)
```bash
for index in {1..3}; do echo $index; done
for i in {1..3}; do for j in {a..c}; do echo $i$j; done; done
for i in {12..14}; do for j in {A..H}; do echo $i$j; done; done
# C-style
for ((i=1; i<10; i++)); do echo $i; done
```

#### Loop over lists
```bash
for i in `ls`; do echo The following file/folder exists: $i; done
```

#### Iterate over an array

```bash
declare -a communities=(1 2 5 6 8 9 10 11)

for community in ${communities[@]}
do
    echo $community
done
```

* Length of an array `${#array[@]}`

#### Iterate over two arrays using a single index (such as when using an array job on a cluster).  

```bash
declare -a parameters1=(0.0 0.2 0.4 0.6 0.8 1.0 1.2 1.4 1.6 1.8 2.0)
declare -a parameters2=(1350 1400 1450 1500 1550 1600 1650 1700 1750 1800 1850)

# Use square brackets to find combined length
combinations=$[${#parameters1[@]} * ${#parameters2[@]}]

for ((c=0; c<$combinations; c++))
do
    echo "-----------------"
    echo $c
    x=`expr $c % ${#parameters1[@]}`
    echo ${parameters1[$x]}
    
    y=`expr $c / ${#parameters1[@]}`
    echo ${parameters2[$y]}
done

# Alternatively (for 1 to a number) could use the following ... 
for c in $(seq 1 $combinations) 
do
    echo $c
done
```

Extra looping ideas ... 

Using `seq`: 
```bash
start=10; stop=13
for index in $(seq $start $stop); do echo $index; done

start="A"; stop="D"
for index in $(seq $start $stop); do echo $index; done
```

```bash
seq -s "," -w 0 .05 .1
```

while loops

if statements
elif statements
else statements

xargs

# Organising files


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


Functions in bash

```bash
function copy_parameters {
    inputdir=$1
    outputdir=$2
    echo "Parameter 1: $inputdir"
    echo "Parameter 2: $outputdir"
}
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



# Interfacing with Python


Input arguments to Python



Inputting a bash array into Python with `argparse`



