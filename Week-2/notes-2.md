# Week 2

## Datacamp: Intro to Bash Scripting
1 Bash scripting:
- Searching a book with shell: `cat two_cities.txt | egrep 'Sydney Carton|Charles Darnay' | wc -l`
- Shebang: absolute path to Bash interpreter (#!)
- Bash script usually begins with #!/bin/bash  
- Run by: `bash script_name.sh` OR `./eg.sh` if script contains shebang & path to Bash

Shell pipelines to Bash scripts:
```
#!/bin/bash
cat soccer_scores.csv | cut -d "," -f 2 | tail -n +2 | sort | uniq -c
```
sed:
- `cat soccer_scores.csv | sed 's/Cherno/Cherno City/g' | sed 's/Arda/Arda United/g' > soccer_scores_edited.csv`

Standard streams & arguments
- STDIN-STDOUT-STDERR
- `2> /dev/null` in script calls; redirecting STDERR to be deleted
- `1> /dev/null` for STDOUT
- ARGV: array of all arguments given to the program
  - Each argument can be accessed via $
```
echo $1
echo $2
echo $@
echo “There are “ $# “arguments”
```
```
# E.g.
echo $1 
cat hire_data/* | grep "$1" > "$1".csv
```
2 Basic variables
- Single quotes: Shell interprets what is between literally
- Double quotes: Shell interprets literally *except* using `$` and backticks
- Backticks: Shell runs command and captures STDOUT back into a variable (shell within a shell)
```
now_var='NOW'
now_var_singlequote='$now_var'
echo $now_var_singlequote
# $now_var

now_var_doublequote="$now_var"
echo $now_var_doublequote
# NOW

rightnow_doublequote="The date is `date`."
echo $rightnow_doublequote
# The date is Mon 2 Dec 2019 14:13:35 AEDT.

rightnow_parentheses="The date is $(date)." # gives same answer
```
Numeric variables
- `expr 1 + 4` but expr can't handle decimals
- `echo "5 + 7.5" | bc` 
- `echo "scale=3; 10 / 3" | bc` scale is for decimal places
- Beware that `age="6"` wil work, but makes it a string (better `age=6`)
- `expr 5 + 7` is the same as `echo $((5 + 7))` but still no decimals
```
# E.g. 1
temp_f=$1
temp_f2=$(echo "scale=2; $temp_f - 32" | bc)
temp_c=$(echo "scale=2; $temp_f2 * 5 / 9" | bc)
echo $temp_c
```
```
E.g. 2
model1=87.65
model2=89.20
echo "The total score is $(echo "$model1 + $model2" | bc)"
echo "The average score is $(echo "($model1 + $model2) / 2" | bc)"
```
```
E.g. 3
temp_a=$(cat temps/region_A)
temp_b=$(cat temps/region_B)
temp_c=$(cat temps/region_C)
echo "The three temperatures were $temp_a, $temp_b, and $temp_c"
```
Arrays
1. Declare without adding elements: `declare -a my_first_array` then append
2. Create and add elements at the same time: `my_first_array=(1 2 3)`
   - Use spaces not commas!
- `echo ${my_array[@]}` 
- `echo ${#my_array[@]}` shows length of array
```
my_first_array=(15 20 300 42)
echo ${my_first_array[2]}
# 300 (cuz Bash uses zero-indexing for arrays unlike R)
```
- `my_first_array[0]=999` to set array elements
- `array[@]:N:M` to get subset of array, where `N` is starting index & `M` is how many elements to return
- `array+=(elements)` to append to arrays (must add parentheses)
<br>

- Associative array: with key-value pairs, not numerical indexes
```
declare -A model_metrics=([model_accuracy]=98 [model_name]=""knn"" [model_f1]=0.82)
echo ${model_metrics[@]}
echo ${!model_metrics[@]} # Print keys
```
```
E.g.
temp_b="$(cat temps/region_B)"
temp_c="$(cat temps/region_C)" #variables
region_temps=($temp_b $temp_c) #array
average_temp=$(echo "scale=2; (${region_temps[0]} + ${region_temps[1]}) / 2" | bc)
region_temps+=($average_temp) #append
echo ${region_temps[@]}
```
3 Control Statements

```
x="Queen"
if [ $x == "King" ]; then
    echo "$x is a King!"
else
    echo "$x is not a King!"
fi
```
- Put spaces btwn square brackets & conditional elements inside
- Semi-colon after close-bracket `];`
- Arithmetic IF statements can use the double-parenthesis structure:
```
x=10
if (($x > 5)); then
    echo "$x is more than 5!"
fi
```
- Arithmetic IF statements can also use square brackets & flag:
  - `-eq` =,`-ne` ≠, `-lt` <, `-le` ≤, `-gt` >, `-ge` ≥
  
- Other conditional flags:
  - `-e` if file exists, `-s` if file exists & has size greater than zero, `-r` if file exists & is readable, `-w` if file exists & is writable
  <br>

- `&&` AND
- `||` OR
- Chain conditionals:
```
x=10
if [ $x -gt 5 ] && [ $x -lt 11 ]; then
    echo "$x is more than 5 and less than 11!"
fi
```
- Or double-square-bracket notation:
```
x=10
if [[ $x -gt 5 && $x -lt 11 ]]; then
    echo "$x is more than 5 and less than 11!"
fi
```
- Can also use many command-line programs directly in the conditional, removing the square brackets
- Or call a shell-within-a-shell:
```
# -q for quiet grep
if $(grep -q Hello words.txt); then
    echo "Hello is inside!"
fi
```
```
E.g. 1
# Extract Accuracy from first ARGV element
# .* matches any character (except for line terminators)
# // repeats the last regular expression match
accuracy=$(grep Accuracy $1 | sed 's/.* //')

# Conditionally move into good_models folder
if [ $accuracy -ge 90 ]; then
    mv $1 good_models/
fi

# Conditionally move into bad_models folder
if [ $accuracy -lt 90 ]; then
    mv $1 bad_models/
fi
```

FOR loops & WHILE statements
- Brace expansion: {START..STOP..INCREMENT}
```
for x in {1..5..2}
do
    echo $x
done
```
```
# Three expression syntax:
for ((x=2;x<=4;x+=2))
do
     echo $x
done
```
```
# Glob expansions
for book in books/*
do
      echo $book
done
```
```
# Shell within a shell
for book in $(ls books/ | grep -i 'air')
do
      echo $book
done
```
- WHILE statement syntax is similar to FOR loop, but set condition which is tested at each iteration
  - Surround condition in square brackets; use flags etc.
- Ensure there is a change inside the code to trigger stop (if not infinite loop)
```
x=1
while [ $x -le 3 ];
do
    echo $x
    ((x+=1))
done
```
```
# E.g.
for file in robs_files/*.py
do  
    if grep -q 'RandomForestClassifier' $file ; then
        mv $file to_keep/
    fi
done
```
Case statements
- Can be better than IF statements when have multiple/complex conditionals
- Basic CASE statement format:
```
case 'STRINGVAR' in
  PATTERN1)
  COMMAND1;;
  PATTERN2)
  COMMAND2;;
  *)
  DEFAULT COMMAND;;
esac

```
- Can use regex for the PATTERN (e.g. `Air*` for 'startswithAir' or `*hat*` for 'contains hat'.
- Separate pattern & code to run by a close-parenthesis and finish commands with double semi-colon
- `*) DEFAULT COMMAND;;` is common (but not required) to finish with a default command that runs if none of the other patterns match
- `esac` to finish ('case' spelled backwards)

```
# E.g.
case $(cat $1) in
  *sydney*)
  mv $1 sydney/ ;;
  *melbourne*|*brisbane*)
  rm $1 ;;
  *canberra*)
  mv $1 "IMPORTANT_$1" ;;
  *)
  echo "No cities found" ;;
esac
```
```
for file in model_out/*
do
    case $(cat $file) in
      *"Random Forest"*|*GBM*|*XGBoost*)
      mv $file tree_models/ ;;
      *KNN*|*Logistic*)
      rm $file ;;
      *) 
      echo "Unknown model in $file" ;;
    esac
done
```

4 Functions & Automation
- Bash function:
```
function_name () {
    #function_code
    return #something
}
```
- alternate structure:
```
function function_name {
    #function_code
    return #something
}
```
Arguments, return values, and scope
```
function print_filename () {
    echo "The first file was $1"
    for file in $@
    do
        echo "This file has name $file"
	done
}
print_filename "LOTR.txt" "mod.txt" "A.py"
```
- Scope: global or local
- All variables in Bash are global by default
- Use `local` to restrict scope
<br>

- `return` is only meant to determine if function was a success (0) or failure (1-255)
- It is captured in the global variable `$?` 
- Our options are:
- 1. Assign to a global variable
- 2. `echo` what we want back (last line in function) and capture using shell-within-a-shell
```
function convert_temp {
    echo $(echo "scale=2; ($1 - 32) * 5 / 9" | bc)
}
converted=$(convert_temp 30)
echo "30F in Celsius is $converted C"
```
```
E.g.
function return_percentage () {
  percent=$(echo "scale=2; 100 * $1 / $2" | bc)
  echo $percent
}
return_test=$(return_percentage 456 632)
echo "456 out of 632 as a percent is $return_test%"
```
```
E.g. global
function get_number_wins () {
  win_stats=$(cat soccer_scores.csv | cut -d "," -f2 | egrep -v 'Winner'| sort | uniq -c | egrep "$1")
}
get_number_wins "Etar"
echo "The aggregated stats are: $win_stats"
```
```
E.g. local
function sum_array () {
  local sum=0
  for number in "$@"
  do
    sum=$(echo "$sum + $number" | bc)
  done
  echo $sum
  }
test_array=(14 12 23.5 16 19.34)
total=$(sum_array "${test_array[@]}")
echo "The total sum of the test array is $total"
```
Schedule scripts with Cron
- `cron` is driven by a `crontab`, which is a file that contains `cronjobs`, which each tell `crontab` what code to run and when
- `crontab -l` to see what schedules (`cronjobs`) are currently programmed
- There are 5 stars to set, 1 for each time unit: minute, hour, day of month, month, day of week
- The default `*` runs at 'every' interval
  - `15 14 * * 7 bash myscript.sh` run at 2:15pm every Sunday
  - Use comma for specific intervals: `15,30,45 * * * *` run at 15, 30, 45 minute at everyhour, everyday etc.
  - Use slash for 'every X increment': `*/15 * * * *` runs every 15 mins at every hour,day etc.
- `crontab -e` to edit list of cronjobs

## SSH
SSH config entry example:
```
ForwardAgent yes
Host hostname
  Hostname hostip
  IdentityFile pathtokey
  User jovena
```
- `ssh overdrive`
- `scp testfile.txt jovena@overdrive:/home/jovena`
- remember to `exit` session & disconnect vpn
  
## VCF - Variant Call Format
- https://gatk.broadinstitute.org/hc/en-us/articles/360035531692-VCF-Variant-Call-Format
- Header: ##fileformat, ALT, FILTER, FORMAT, INFO, contig, reference
- Records: #record headers

