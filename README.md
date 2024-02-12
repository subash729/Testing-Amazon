# 1. Basics
Copy your `/etc/passwd` file to `/tmp` and write a script to display the following:


```
cp /etc/passwd /tmp
ls -al /tmp | grep pass
```
### 1.1 Full contents of the file sorted in ascending order (display the whole file) 

```
cat passwd | sort
```
### 1.2 Counting Total number of lines in passwd file
```
wc -l passwd
```

### 1.3 Counting Total number of word in passwd file
```
wc -c passwd
```
### 1.4 Filtering usernames that contains the letter ``` s ```
```
cat passwd | grep -E "^" | cut -d ":" -f 1 | grep s
```
The Agile software development model is a method of developing software by breaking 

### 1.5 Filtering usernames that starts with the letter ``` r ```
```
cat passwd | grep -E "^r" | cut -d ":" -f 1
```

<div style='text-align: justify;'>
During this phase, we prepare a report of idea to develop the complete system development task. We generally study feasiblility of resources like time and budget required to built the system.A strategy is established to idetify taske <b>that will performed in each step by identifying the risk. </b> We focus on completing the company targets and determining how to achieve them via prioritizing the requirement as project goes on. A development plan is prepared to identify major areas and propose system design, as well as identify and update risks.
</div>

