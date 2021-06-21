# Week 1

## Basics (Youtube)
Vid 1:
- `pwd`
- `ls`
- `cd`
- `ls -l -h`
- `mkdir`
- `touch` creates new file
- `cp`
- `mv`
- `rm` be careful when removing
- `rm -i -r` for removing directories and confirming each deletion

Vid 2:
- `echo ‘Hello world’ > testfile`
- `cat testfile >> newfile` (>> to append not overwrite )
- `less newfile` to show content in pages
- `q`
- `more` older version of less
- `mv Desktop/newfile .` 
- `mv newfile testfile Desktop`
- `mv Desktop/*file`
- `nano`
- `wget` installed from brew for mac

Vid 3:
- `head -n 10 Q9BYF1.txt | tail -n 5` piping
- `man`
- `history`
- `exit`
- ctrl + r *(reverse-i-search)*
  - `!196`
- press tab twice *(to list possible commands/files)


## Datacamp: Intro to Shell
1.
- `ls -R -F` to list subdirectories and display filetype
- `cut -f 2-5,8 -d , values.csv`
- `grep`
- `grep ‘p’ fruits.txt`
- `grep ‘[pc]’ fruits.txt` lists if have p and/or c
- `egrep 'Sydney Carton|Charles Darnay'` either of those
2.
- `tail -n 5 seasonal/winter.csv > last.csv`
- `cut -d , -f 2 seasonal/summer.csv | grep -v Tooth | head -n 1` delimited with comma, 2nd column, inverse Tooth
- `grep 2017-07 seasonal/spring.csv | wc -l ` count lines
- wildcards: *  ?	[…]	{…}
3.
- `sort -r` sort in reverse
- `uniq -c` include no. of times line occurred
- `cut -d , -f 2 seasonal/winter.csv | grep -v Tooth | sort | uniq -c`
- `wc -l seasonal/* | grep -v total | sort -n | head -n 1`
*Environment variables*: System-wide
- `set | grep USER`
- `echo $USER`
*Shell variables*: Current shell
- `testing=seasonal/winter.csv`
- `head -n 1 $testing`
*Loops*
- for …variable… in …list… ; do …body… ; done
- `for filetype in gif jpg png; do echo $filetype; done`
- `files=seasonal/*.csv`
- `for f in $files; do echo $f; done`
- `for file in seasonal/*.csv; do grep 2017-07 $file | tail -n 1; done`
4.
- `cp seasonal/s* ~`
- `grep -h -v Tooth spring.csv summer.csv > temp.csv`
- `history | tail -n 3 > steps.txt`
- `nano dates.sh`
- `bash dates.sh`
*Shell script*
- `$@` *(dollar sign immediately followed by at-sign) to mean "all of the command-line parameters given to the script".*
- `tail -q -n +2 $@ | wc -l`
- `bash count-records.sh seasonal/*.csv > num-records.out`
- $1, $2, etc. are *positional parameters*
- `head -n $2 $1 | tail -n 1 | cut -d , -f $3`
- `bash get-field.sh seasonal/summer.csv 4 2`
5.
- `wc -l $@ | grep -v total | sort -n -r | head -n 1`
- ctrl + k to copy; ctrl + u to paste
*To print the first and last data records of each file:*
```
for filename in $@
do
    head -n 2 $filename | tail -n 1
    tail -n 1 $filename
done
```
- ctrl + c: stops program running
- `sed 's/unix/linux/' geekfile.txt`
- Add /g or /1 or /2 etc.


## Datacamp: Intro to Github
1.
- `git status`
- `git diff `
- `git add filename.txt` to add file in staging area
- `git diff -r HEAD data/northern.csv`
- `git commit -m "Program appears to have become self-aware."`
- `git commit --amend - m "new message"`
- `git log`
- `git log data/sourthern.csv`
2.
- `git show` first few characters of commit’s hash
HEAD: refers to most recent commit
- `git show HEAD~1`
- `git annotate filename.txt`
- `git diff HEAD~1..HEAD~3`
3.
- `.gitignore` can store wildcards in this
- `git clean` removes untracked files
- `git clean` -n *shows list* / -f *deletes them*
- `git config --list` can add --local/global/system
- `git config --global` can add *setting* *value*
4.
- `git reset HEAD path/to/file` to unstage
git checkout -- path/to/file
- `git checkout 2242bd report.txt`
- `git log -3 report.txt`
- `git reset` *HEAD is the default*
- `git checkout -- .`
5.
- `git branch`
- `git diff branch-1..branch-2`
- `git checkout summary-statistics` to switch branches
- `git rm filename.txt`
- `git checkout -b branch-name` create & switch branch
- `git merge summary-statistics master` from source to destination
  - If conflict emerges, use git status and edit nano the file and remove markers
6.
- `git init project-name`
- `git clone URL newprojectname`
- `git clone /home/thunk/repo dental`
- `git remote -v`
- `git remote add remote-name URL`
- `git remote rm remote-name`
7.
- `git pull remote-name branch-name`
- `git push remote-name branch-name`
  - If push fails, pull the remote and retry it
- `git fetch` safer to download content from remote since it does not merge

