## 3. Cron

Write a cron job that runs every hour. It should:

- Fetch the HTML response from `https://www.example.com`
- Log the output inside this directory `/var/log/bash_assignment/`
- Compress all previous logs as a ZIP archive
- Keep only 5 most-recent archives and delete all other archives

## Answer
I had write a scipt to accomplish the task and scipt is run every hour
```
 touch cronscript.sh
 chmod +x cronscript.sh
 vi cronscript.sh
 ./cronscript.sh
```
script used
```bash

#!/bin/bash

#Defining some Variable as per question
Web_download=$(wget https://www.example.com)
Log_Dir="/var/log/bash_assignment"
Archieve_Dir="$Log_Dir/archives"

#Creating seperate Directory for log and Archieve
mkdir -p "$Log_Dir"
mkdir -p "$Archieve_Dir"

#Creating file template baed on date and time to store output during web request
Log_File="$Log_Dir/$(date +\%Y\%m\%d_\%H\%M).log"
echo "$web_download" > "$Log_File"

#Finding all the log file under log directoy and preparing zip
#step-1 finding log file
log_files=$(find "$Log_Dir" -name "*.log")

# step - 2 preparing zip template
Zip_File="$Archieve_Dir/archive_$(date +\%Y\%m\%d_\%H\%M).zip"

#step - 3 Zipping log files
echo "$log_files" | xargs -I {} zip -j "$Zip_File" {}


# Keep only 5 most recent archives and delete others
#step -1 finding all list
archive_list=$(ls -t "$Archieve_Dir")

#step - 2 find list to delete files
archives_to_delete=$(echo "$archive_list" | tail -n +6)

#step - 3 deleting the files
echo "$archives_to_delete" | xargs -I {} rm "$Archieve_Dir/{}"
```
OPening crontab
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-12-bash-scripting-pranav-subash729/blob/main/materials/T3-Q0-opening-crontab.jpg">
</p>

Configuring Cron
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-12-bash-scripting-pranav-subash729/blob/main/materials/T3-Q1-crontab-script-everyhour.jpg">
</p>

Restarting Cron
after configuring cron
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-12-bash-scripting-pranav-subash729/blob/main/materials/T3-Q-restarting%20cron%20after%20configuration.jpg">
</p>

Zip and log files
<p align="center">
<img src="https://github.com/LF-DevOps-Training/feb-12-bash-scripting-pranav-subash729/blob/main/materials/T3-Q-creaing%20log%20and%20acrhieve.jpg">
</p>


<div style='text-align: justify;'>
During this phase, we prepare a report of idea to develop the complete system development task. We generally study feasiblility of resources like time and budget required to built the system.A strategy is established to idetify taske <b>that will performed in each step by identifying the risk. </b> We focus on completing the company targets and determining how to achieve them via prioritizing the requirement as project goes on. A development plan is prepared to identify major areas and propose system design, as well as identify and update risks.
</div>

