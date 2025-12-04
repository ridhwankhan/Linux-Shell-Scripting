# 🐧 Hands-on Introduction to Linux Commands and Shell Scripting
## **IBM & Coursera Final Project: Automated Backup Script**

<p align="center">
  <img src="https://img.shields.io/badge/Course-IBM%20Linux%20Commands-1F7FEE?style=for-the-badge&logo=ibm" alt="IBM Badge">
  <img src="https://img.shields.io/badge/Platform-Coursera-0056D2?style=for-the-badge&logo=coursera" alt="Coursera Badge">
  <img src="https://img.shields.io/badge/Language-Bash%20Shell-4EAA25?style=for-the-badge&logo=gnu-bash" alt="Bash Badge">
  <img src="https://img.shields.io/badge/Automation-Cron%20Job-E99400?style=for-the-badge" alt="Cron Badge">
</p>

---

### 🏆 IBM Certification

Completed a comprehensive project in Bash scripting as part of the **IBM Linux Essentials Certification**.

Certified by IBM after successfully completing the final project.

* **Verification Link:** [Verify at coursera.org/verify/certificate/Y7L2E1488P7W3](https://coursera.org/verify/certificate/Y7L2E1488P7W3)

<p align="center">
  <img src="IBM%20%20linux%20certificate.png" alt="IBM Linux Certificate" width="800"/>
</p>

---

### ⚙️ Linux Automation Project: `backup.sh`

The project involved developing a **Bash script** to automate the backup of critical directories, focusing on system administration tasks like file handling, conditional logic, and scheduling. The script intelligently backs up only files modified in the last **24 hours**.

-   **Developing a Backup Script**: Automated the backup of critical directories using Bash, focusing on environment variables, file handling, and conditional logic.
-   **Cron Job Scheduling**: Implemented cron jobs to run the backup script at regular intervals, ensuring consistent data protection.
-   **System Administration Tasks**: Applied essential Linux commands for file management and archiving.

---

### 💻 The Script Code: `backup.sh`

```bash
#!/bin/bash

# This checks if the number of arguments is correct
# If the number of arguments is incorrect ( $# != 2) print error message and exit
if [[ $# != 2 ]]
then
  echo "backup.sh target_directory_name destination_directory_name"
  exit 1
fi

# This checks if argument 1 and argument 2 are valid directory paths
if [[ ! -d $1 ]] || [[ ! -d $2 ]]
then
  echo "Invalid directory path provided"
  exit 1
fi

# [TASK 1] Set target and destination directories from arguments
targetDirectory=$1
destinationDirectory=$2

# [TASK 2] Display directory names
echo "target directory $1"
echo "destination directory $2"

# [TASK 3] Get current Epoch Timestamp
currentTS=$(date +%s)

# [TASK 4] Define backup file name with unique timestamp
backupFileName="backup-$currentTS.tar.gz"

# We're going to:
  # 1: Go into the target directory
  # 2: Create the backup file
  # 3: Move the backup file to the destination directory

# To make things easier, we will define some useful variables...

# [TASK 5] Get the original absolute path
origAbsPath=$(pwd)

# [TASK 6] Go to destination, get its absolute path
cd $destinationDirectory
destDirAbsPath=$(pwd)

# [TASK 7] Go back to original path, then into the target directory
cd $origAbsPath
cd $targetDirectory

# [TASK 8] Calculate yesterday's timestamp (24 hours ago)
yesterdayTS=$(($currentTS - 24 * 60 * 60))

declare -a toBackup

for file in $(ls) # [TASK 9] List all files/directories
do
  # [TASK 10] Check if file was modified in the last 24 hours
  if [[ $(date -r "$file" +%s) > $yesterdayTS ]]
  then
    # [TASK 11] Add eligible file to the array
    toBackup+=("$file")
  fi
done

# [TASK 12] Create the compressed archive
if [ ${#toBackup[@]} -eq 0 ]; then
  echo "No files modified in the last 24 hours. No backup created."
  exit 0
fi

tar -czvf $backupFileName "${toBackup[@]}"

# [TASK 13] Move the backup file to the destination directory
mv $backupFileName $destDirAbsPath
