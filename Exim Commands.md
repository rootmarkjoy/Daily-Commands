### How to Find a spam script location with Exim:-
===================================================

Run the following command to pull the most used mailing script's location from the Exim mail log:-
```sh
grep cwd /var/log/exim_mainlog | grep -v /var/spool | awk -F"cwd=" '{print $2}' | awk '{print $1}' | sort | uniq -c | sort -n
```
#### It would help if you got back something like this:
15 /home/userna5/public_html/about-us <br>
25 /home/userna5/public_html <br>
7866 /home/userna5/public_html/data <br>

#### We can see that /home/userna5/public_html/data has far more deliveries coming in than any others.


#### Now we can run the following command to see what scripts are located in that directory:-
```sh
ls -lahtr /userna5/public_html/data
```

#### In thise case we got back:
drwxr-xr-x 17 userna5 userna5 4.0K Jan 20 10:25 ../ <br>
-rw-r--r-- 1 userna5 userna5 5.6K Jan 20 11:27 mailer.php <br>
drwxr-xr-x 2 userna5 userna5 4.0K Jan 20 11:27 ./ <br>

#### So we can see there is a script called mailer.php in this directory

==============================================

#### Knowing the mailer.php script was sending mail into Exim, we can now take a look at our Apache access log to see what IP addresses are accessing this script using the following command:-
```sh
grep "mailer.php" /home/userna5/access-logs/example.com | awk '{print $1}' | sort -n | uniq -c | sort -n
```

#### You should get back something similar to this:

2 123.123.123.126 <br>
2 123.123.123.125 <br>
2 123.123.123.124 <br>
7860 123.123.123.123 <br>

#### We can see the IP address 123.123.123.123 was using our mailer script in a malicious nature.

==============================================

#### If you find a malicious IP address sending a large volume of mail from a script, you'll probably want to go ahead and block them at your server's firewall so that they can't try to connect again.

#### This can be accomplished with the following command:
```sh
apf -d 123.123.123.123 "Spamming from script in /home/userna5/public_html/data"
```

==============================================

#### Run the following command to locate duplicate subjects from your Exim mail log:-
```sh
awk -F"T=\"" '/<=/ {print $2}' /var/log/exim_mainlog | cut -d\" -f1 | sort | uniq -c | sort -n
```

#### You should get back something that looks like this:

285 Out of Office <br>
303 [Forum reply] Please moderate <br>
578 New Account <br>
1764 Melt Fat Naturally <br>

#### So in this case we can see that by far the subject Melt Fat Naturally is the most duplicated subject currently in the Exim mail log.

==============================================

#### Now we can search to see what user has been sending out this possible spam message with the following command:-
```sh
grep "Melt Fat Naturally" /var/log/exim_mainlog | awk '{print $6}' | sort | uniq -c | sort -n
```

#### You should end up with some results like this:

1 test@example.com <br>
1762 user01@example.com <be>

#### So in this case we can see that it looks like the user01@example.com account was used to relay this spam message.

==============================================

#### You can now locate all of the IP addresses the user01@example.com account has been sending mail from, and possibly block them at your server's firewall if the activity looks malicious to you.

#### Use the following command to find all the IP addresses the account has been relaying mail with:-
```sh
grep "<= user01@example.com" /var/log/exim_mainlog | grep "Melt Fat Naturally" | grep -o "\[[0-9.]*\]" | sort -n | uniq -c | sort -n
```

#### You should get back something related to this:

1762 [123.123.123.123]

#### So we can see that all 1,763 messages the user01@example.com user sent out, all came from the same 123.123.123.123 IP address.

==============================================

#### Now we can go ahead and block this IP address from our server at the server's firewall by running the following command:-
```sh
apf -d 123.123.123.123 "Sending weight loss spam from user01@example.com"
```

==============================================

```sh
exiqgrep -i -f | xargs exim -Mrm                                            =====>  (remove all mail with frozen)
/usr/sbin/exim -bpr | grep frozen | awk '{print $3}' | xargs exim -Mrm      =====>  (Remove frozen mails)
exim -bpr| grep domainname@domain.com| awk '{print $3}'|xargs exim -Mrm     =====>  (Remove mail queue for particular email account)
exim -bp | grep -B1 gmail | tail -20                                        =====>  (List of the Gmail ID to delivered)
```

==============================================

### How to use the eximstats utility to generate reports on emails processed by the server.

#### The easiest way to get started with the Eximstats utility is to simply run the following command via SSH as the root user on your cPanel server:
```sh
eximstats -txt /var/log/exim_mainlog
```

#### This is an example of a text report where everything but the section headings have been filtered out. Your server's report will be filled with actual data:
```sh
[root@srv001 ~]# eximstats -txt /var/log/exim_mainlog | grep --group-separator="" -B2 "^-"

Grand total summary
-------------------


Deliveries by transport
-----------------------


Messages received per hour (each dot is 1 message)
--------------------------------------------------


Deliveries per hour (each dot is 2 deliveries)
----------------------------------------------


Time spent on the queue: all messages
-------------------------------------


Time spent on the queue: messages with at least one remote delivery
-------------------------------------------------------------------


Relayed messages
----------------


Top 50 mail rejection reasons by message count
----------------------------------------------


Top 50 mail temporary rejection reasons by message count
--------------------------------------------------------


Top 50 sending hosts by message count
-------------------------------------


Top 50 sending hosts by volume
------------------------------


Top 50 host destinations by message count
-----------------------------------------


Top 50 host destinations by volume
----------------------------------


Top 50 local destinations by message count
------------------------------------------


Top 50 local destinations by volume
-----------------------------------


Top 50 rejected ips by message count
------------------------------------


Top 50 temporarily rejected ips by message count
------------------------------------------------
```

#### If you wanted to generate a report for only the month of September in 2021, you could use the following script to filter out only September's logs and place them into a new log file in the root home directory:
```sh
zgrep -h "2021-09" /var/log/exim_mainlog* | sort -n > /root/exim-mainlog-september-2021.log
```

#### Then, you would be able to use eximstats on that new September only log file to see the statistics.

#### Here we can see that so far for the month of September my testing server has delivered a total of 509 messages and has received 312:
```sh
[root@srv001 ~]# eximstats -txt /root/exim-mainlog-september-2021.log | head -12

Exim statistics from 2021-09-01 00:58:27 to 2021-09-25 23:29:26

Grand total summary
-------------------
At least one address
TOTAL Volume Messages Addresses Hosts Delayed Failed
Received 17MB 312 37 1 0.3% 6 1.9%
Delivered 3463KB 509 509 7
Rejects 1486 603
Temp Rejects 141 7
```

#### It is also possible to merge two reports together. For example if you had these two date ranged reports for the first two parts of September:
```sh
[root@srv001 ~]# zgrep -E -h "2021-09-0[1-9]" /var/log/exim_mainlog* | sort -n > /root/exim-mainlog-september-01-09-2021.log
[root@srv001 ~]# zgrep -E -h "2021-09-1[0-9]" /var/log/exim_mainlog* | sort -n > /root/exim-mainlog-september-10-19-2021.log
```

#### It would be possible to merge those reports into one with eximstats like this.

#### Generate the separate reports:
```sh
[root@srv001 ~]# eximstats -txt=/root/exim-Sep-01-09.report /root/exim-mainlog-september-01-09-2021.log
[root@srv001 ~]# eximstats -txt=/root/exim-Sep-10-19.report /root/exim-mainlog-september-10-19-2021.log
```

#### Then combine the generated reports into a larger range:
```sh
[root@srv001 ~]# eximstats -merge /root/exim-Sep*.report > /root/exim-Sep-01-19.report
```

#### There are many other useful features that you may explore in the manual page for the eximstats utility:

https://linux.die.net/man/8/eximstats 
