#!/bin/bash
PATH=$PATH:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bon:/root/bin

###############################################################################

SCRIPT FOR CALCULATING THE MEMORY USAGE OF A INDIVIDUAL PROCESS
Author : Leegin Bernads T.S

###############################################################################

alias ppid='ps -o ppid= -p'

echo "You can calculate the memory usage of each process from the following list"
echo "............................."
echo "1) httpd"
echo "2) mysql"
echo "3) php"
echo "4) nginx"
echo "............................."
read -p "Enter the process for which you want to calculate the memory : " mem

commands() {
if [ -f /proc/$pid/smaps ]; then
        echo "*****Mem usage ****for PID $pid"     
        rss=$(cat /proc/$pid/smaps | awk 'BEGIN {i=0} /^Rss/ {i = i + $2} END {print i}')
        pss=$(cat /proc/$pid/smaps | awk 'BEGIN {i=0} /^Pss/ {i = i + $2 + 0.5} END {print i}')
        sc=$(cat /proc/$pid/smaps | awk 'BEGIN {i=0} /^Shared_Clean/ {i = i + $2} END {print i}')
        sd=$(cat /proc/$pid/smaps | awk 'BEGIN {i=0} /^Shared_Dirty/ {i = i + $2} END {print i}')
        pc=$(cat /proc/$pid/smaps | awk 'BEGIN {i=0} /^Private_Clean/ {i = i + $2} END {print i}')
        pd=$(cat /proc/$pid/smaps | awk 'BEGIN {i=0} /^Private_Dirty/ {i = i + $2} END {print i}')
        echo "=================================================================="
        echo "--The Resident Set Size(Rss) : $rss kB" 
        echo "-- The Propotional Size Set(Pss) : $pss kB"
        echo "The Shared Clean used by the PID $pid : $sc kB"
        echo "The Shared Dirty used by the PID $pid : $sd kB"
        echo "The total shared pages used by the PID $pid : $(($sc + sd)) kB"
        echo "The private Clean used by the PID $pid : $pc kB"
        echo "The private Dirty used by the PID $pid : $pd kB"
        echo "The total Private pages used by the PID $pid : $(($pd + $pc)) kB"
        echo "=================================================================="
    fi
}

httpd() {
for pid in $(ppid `ps aux | grep httpd | cut -d" " -f3`|uniq);do
commands
num=$(ps aux | grep httpd | grep -v pts | wc -l)
echo "++++++++++++++++++++++++"
echo "The total number of httpd process running in the server: $num"
echo "The total memory used by the httpd processes in KB: $(($num * $(($pc + $pd)))) kB"
echo "The total memory used by the httpd processes in MB: $(($num * $(($pc + $pd))/1024)) MB"
echo "++++++++++++++++++++++++"
done
}

mysql() {
for pid in $(ppid `ps aux | grep mysql | cut -d" " -f3`|uniq);do
commands
num=$(ps aux | grep mysql | grep -v pts | wc -l)
echo "++++++++++++++++++++++++"
echo "The total number of mysql process running in the server: $num"
echo "The total memory used by the mysql processes : $(($num * $(($pc + $pd)))) kB"
echo "The total memory used by the mysql processes in MB: $(($num * $(($pc + $pd))/1024)) MB"
echo "++++++++++++++++++++++++"
done
}

php() {
for pid in $(ppid `ps aux | grep php | cut -d" " -f3`|uniq);do
commands
num=$(ps aux | grep php | grep -v pts | wc -l)
echo "++++++++++++++++++++++++"
echo "The total number of php process running in the server: $num"
echo "The total memory used by the php processes : $(($num * $(($pc + $pd)))) kB"
echo "The total memory used by the php processes in MB: $(($num * $(($pc + $pd))/1024)) MB"
echo "++++++++++++++++++++++++"
done
}

nginx() {
for pid in $(ppid `ps aux | grep nginx | cut -d" " -f3`|uniq);do
commands
num=$(ps aux | grep nginx | grep -v pts | wc -l)
echo "++++++++++++++++++++++++"
echo "The total number of nginx process running in the server: $num"
echo "The total memory used by the nginx processes : $(($num * $(($pc + $pd)))) kB"
echo "The total memory used by the nginx processes in MB: $(($num * $(($pc + $pd))/1024)) MB"
echo "++++++++++++++++++++++++"
done
}

case $mem in
    1)
    httpd
    ;;
    2)
    mysql
    ;;
    3)
    php
    ;;
    4)
    nginx
    ;;
esac
