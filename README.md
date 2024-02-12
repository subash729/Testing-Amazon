# 1. Basics
Copy your `/etc/passwd` file to `/tmp` and write a script to display the following:


```
cp /etc/passwd /tmp
ls -al /tmp | grep pass
```
### 1.1 Full contents of the file sorted in ascending order (display the whole file) 

```
 touch sorting.sh
   69  chmod +x sorting.sh
   70  vi sorting.sh
   71  ./sorting.sh
```
Script used to sort
```
#!/bin/bash

set -x

filepath="/tmp/passwd"

echo "Sorting the content of /etc/passwd"
echo "================sorting started ==============="
sort $filepath
echo "================sorting Finished ==============="
```
### 1.2 Counting Total number of lines in passwd file
```
touch totalline.sh
chmod +x totalline.sh
vi totalline.sh
./totalline.sh
```
Script used
```
#!/bin/bash

filepath="/tmp/passwd"

echo "Please Wait, Counting Total no of Line"
echo "Total no of line is $(wc -l $filepath | cut -d " " -f 1)"
```

### 1.3 Counting Total number of word in passwd file
```
touch totalcharacter.sh
chmod +x totalcharacter.sh
vi totalcharacter.sh
./totalcharacter.sh
```
Script used
```
#!/bin/bash


filepath="/tmp/passwd"

echo "Please Wait, Counting Total no of Character"
echo "Total no of charater is $(wc -c $filepath | cut -d " " -f 1)"
```
### 1.4 Filtering usernames that contains the letter ``` s ```
command used
```
 touch findname.sh
 chmod +x findname.sh
 vi findname.sh
 ./findname.sh
```
Script used
```
#!/bin/bash


filepath="/tmp/passwd"

echo "Please Wait, Searchinf for Username which contains letters"
echo "The list of username are as follows"
echo "====================================="
cat passwd | grep -E "^" | cut -d ":" -f 1 | grep s


```

The Agile software development model is a method of developing software by breaking 

### 1.5 Filtering usernames that starts with the letter ``` r ```
Commands used
```
 touch findusename.sh
 chmod +x findusename.sh
 vi findusename.sh
 ./findusename.sh
```
script used
```
#!/bin/bash


filepath="/tmp/passwd"

echo "Please Wait, Searching for Username which start wih r"
echo "The list of username are as follows"
echo "====================================="
cat passwd | grep -E "^r" | cut -d ":" -f 1
```

<div style='text-align: justify;'>
During this phase, we prepare a report of idea to develop the complete system development task. We generally study feasiblility of resources like time and budget required to built the system.A strategy is established to idetify taske <b>that will performed in each step by identifying the risk. </b> We focus on completing the company targets and determining how to achieve them via prioritizing the requirement as project goes on. A development plan is prepared to identify major areas and propose system design, as well as identify and update risks.
</div>

