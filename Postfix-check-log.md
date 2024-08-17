# POSTFIX COMMANDS TO CHECK LOGS

To check Postfix logs, mail delivery logs, and view the mail queue, you can use the following commands:

Checking Postfix Logs

Postfix logs are usually located in the /var/log directory. The most common log files are mail.log, maillog, and syslog. You can use various commands to view these logs:

Viewing the last few lines of the log:

```sh
tail -f /var/log/mail.log
```

#### or

```sh
tail -f /var/log/maillog
```

Viewing the log with filtering for specific keywords (e.g., "error" or "warning"):

```sh
grep 'error' /var/log/mail.log
```

#### or

```sh
grep 'warning' /var/log/mail.log
```

Using less for a more interactive way to view the logs:
```sh
less /var/log/mail.log
```

You can then use the search feature within less by typing /keyword.

Checking Mail Delivery Logs

Mail delivery logs are part of the same log files as above. You can search for specific delivery status codes, sender/recipient addresses, etc.

To check logs related to a specific email address:
```sh
grep 'user@example.com' /var/log/mail.log
```

To check logs for a specific time frame:
```sh
grep 'Jun 25' /var/log/mail.log
```

Viewing the Mail Queue

Postfix maintains a mail queue, which you can view and manage with the following commands:

To view the mail queue:
```sh
postqueue -p
```

To flush the mail queue (attempt to deliver all queued mail):
```sh
postqueue -f
```

To view detailed information about specific queued messages:
```sh
postcat -vq QUEUE_ID
```

Replace QUEUE_ID with the actual ID of the queued message.

Additional Useful Commands

To delete all messages in the queue:
```sh
postsuper -d ALL
```

To delete a specific message from the queue:
```sh
postsuper -d QUEUE_ID
```

To requeue a message:
```sh
postsuper -r QUEUE_ID
```

Make sure you have appropriate permissions (typically root) to access and manage these logs and the mail queue.
