# MySQL UTF-8 issues
A few months ago I upgraded the machine where this website runs from a machine with Ubuntu 14 to one running Ubuntu 19. Because I was coming from a very old version of Ubuntu I decided to create a brand new virtual machine with Ubuntu 19 and move the content myself rather than letting Ubuntu attempt to migrate my stuff. 

This was a good idea because a lot has changed from Ubuntu 14 to Ubuntu 19, plus it gave me the chance to start from a clean slate. However, in doing the migration manually I missed the step to configure the MySQL database to use UTF-8 and I left it instead with the default of Latin 1. 

This resulted in a few of the blogs displaying a bunch strange characters for things that are not part of the Latin 1 character set, for example several of the blogs where showing `Â` or `â€™` and you'll see  `Iâ€™ve` instead of  `I've`.

When I saw this I knew right away that the problem was character encoding in the database, but I wasn't sure how to fix the data. Turns out there were two steps that I had to take. 

## Step 1 - configure the database

The first step was to configure the database to use UTF-8 character set. This was pretty straight forward. From the MySQL prompt I issued the following command to see what the current character set was, notice that it reported `latin1`:

```
mysql> SELECT @@character_set_database, @@collation_database;
+--------------------------+----------------------+
| @@character_set_database | @@collation_database |
+--------------------------+----------------------+
| latin1                   | latin1_swedish_ci    |
+--------------------------+----------------------+
```

Then I changed the character set to `utf8` via the `ALTER DATABASE` command:

```
mysql> ALTER DATABASE blogdb CHARACTER SET utf8 COLLATE utf8_general_ci;
Query OK, 1 row affected (0.00 sec)

mysql> SELECT @@character_set_database, @@collation_database;
+--------------------------+----------------------+
| @@character_set_database | @@collation_database |
+--------------------------+----------------------+
| utf8                     | utf8_general_ci      |
+--------------------------+----------------------+
```

## Step 2 - update the data

Setting the character set as I did above makes sure that the database uses UTF-8 as I save new data into it, but it did not update the records already stored on it. I found [this answer](https://stackoverflow.com/a/9305294/446681) in StackOverflow that shows how to convert the existing data from one character set to another. 

In my case I ran a SQL script that converted the `contentMd` field to `binary` and then to `utf8` and that took care of the issue:

```
mysql> update blogs set contentMd = convert(convert(contentMd using binary) using utf8);
```

## Final words
Of course, I backed up the database before and after I did these steps, just in case, but so far, it looks like the data now is handled correctly. 

I also updated my server notes to remind me to configure the character set if I ever setup a brand new Ubuntu box. You can see my Ubuntu Server configuration notes on this [GitHub repo](https://github.com/hectorcorrea/ubuntuserver).