---
layout: post
status: publish
published: true
title: Script for backing up multiple sites over S/FTP
author: DJ Madeira
author_login: webmaster
author_email: djmadeira@me.com
wordpress_id: 596
wordpress_url: http://djmadeira.com/?p=596
date: '2015-03-07 19:31:42 -0500'
date_gmt: '2015-03-08 00:31:42 -0500'
tags: 
- archived
comments: []
---
There are tons of scripts out there for downloading files automatically over FTP. Unfortunately, most of them are too specific. Either they only work for one site, or they expect CPanel to be available on the server, or they require you to edit the script itself as the config file... nonsense, all of it.

So, as so often happens, I took matters into my own hands. I wrote a script that let me use an external configuration file to back up multiple sites, with different protocols, hosts &amp; logins. <a href="https://github.com/djmadeira/multi-site-ftp-backup-script">You can download it here</a>.

The first version I made worked, but not well; I've since re-written it from scratch, and now I think it's good enough to talk about here.

<h2><a href="http://inception.davepedu.com/noflash.php">Inception</a></h2>

Once upon a time, my company was paying ManageWP to back up our sites. But since our hosting company, WP Engine, had rightly disabled the system() php function, they had to make the backup files using a PHP script. It was buggy as [insert any four letter word here], and <strong>more</strong> than half the time the backups would simply fail.

One day I decided enough was enough. We're paying them all this money, and our sites still aren't even backed up! This should be easy, I'll just write a little script.

My initial requirements were pretty simple:

<ul>
<li>A single backup</li>
<li>of an unlimited number of sites</li>
<li>with an simple configuration file</li>
<li>using per-site logins, passwords, and host names</li><br />
</ul>

I had been itching to do something in C, and this seemed like the perfect candidate. A dead-simple text parser. Then I'd write a bash script to read the config file and pass the values to a command-line FTP client. A great first project.

<h2>Version the first</h2>
As a brief aside, for anybody interested in leaning their first "low-level" language, may I suggest C? It's not as difficult as some have portrayed, and it will make you feel like a boss. It's also a truly beautiful language. I learned using <a href="http://c.learncodethehardway.org/book/">this excellent free course</a>. I bet you'll love it if you're a (computer) speed junky like me.

Anyway, the first implementation was as simple as I could make it. The file format was one site per line, with each field separated by a space. None of the fields would require spaces, so there was no character escape. Each site had only three fields: the directory name, the login, and the host. The login had the username and the password separated by a comma, but I didn't do any parsing with it; I passed it verbatim to the FTP program.

The parser wound up being <160 SLOC. I struggled with a many (read: every) parts of it, and I'm sure it was as leaky as the Apple supply chain rumor mill (I didn't run Valgrind on it), but not bad for my first C project.

I chose <a href="http://lftp.yar.ru">LFTP</a> as the FTP client; it's a really solid program with a ton of options and years of trust behind it, and reasonably sane configuration syntax.

The bash script was essentially an eternal while loop that would cycle through the sites and trigger LFTP's mirror command, so only changed files would be transferred. This worked acceptably, but it was annoying to kill (^C would quit the active LFTP and trigger the next one; even holding ^C wouldn't kill the script). It met the requirements, but it created some problems since it would cancel shutdown and didn't start up automatically.

I used version 1 for a few weeks, and it worked, but there were a lot of limitations. The file format was too limited and hard to extend, and the shutdown/startup/infinite run issues where a no-go for any sort of production environment.

<h2>Version the second</h2>
For the next version I had identified a few new requirements, on top of the old ones:

<ul>
<li>Run in the background, run to completion, and schedule startup automatically</li>
<li>The config file format must have named fields, so that adding fields in the future is easier and doesn't break everything</li>
<li>File format must support more complex field values</li>
<li>Must be able to keep multiple versions of backups</li><br />
</ul>
I also had a few design choices I wanted to make differently:
<ul>
<li>Parser should be content-agnostic; just parse and return the field values</li>
<li>Better error checking and reporting, in script and parser</li><br />
</ul>

For the new parser, I decided CSV would be a good file format. I had already written a CSV parser in PHP, but for this project I decided to use <a href="http://libcsv.sourceforge.net">libcsv</a> and just make a command-line wrapper. I thought that would be easier, but it wound up being more code than the original parser, although not as much as it would have been if I had written a CSV parser from scratch.

The design of <a href="https://github.com/djmadeira/csvr">csvr</a> (short for CSV read) is simple. You give it a CSV file, a row index, a column index (or a column name), and a file name, and it prints the value. If you give it the optional -s <value> flag, it will write that value to the row/column offset you specified. If you don't specify a file, then it reads from the standard input.

Once I got into how libcsv actually works, the saving-a-value part seemed a little more daunting, and since I didn't actually need it for this script, I left it unimplemented. I am still interested in finishing that and making csvr a proper utility that I can use for other projects. It would also be nice to add some other options, for example getting all the values in a row/column, but I'll save that for another commit.

The CSV spec defines that the first row of a CSV is (optionally) the header row, and this is the main reason I picked CSV as the format. I wanted to be able to add more options in the future, and not break the script because I was accessing them by index rather than a name.

I took the opportunity to add some new fields: the remote dir to download, in case the initial directory for the FTP user isn't the one you want to download, the number of backups to keep, and any flags to pass to LFTP's mirror command (mostly to allow regex file excludes.)

The backup shell script was also redone from scratch. This time it would quit when it finished all the sites, was more configurable (you could give it the location of your CSV and backups folder, rather than those being assumed), and gave you better feedback about problems.

A problem I ran into when I tried the script with more edge cases is that LFTP simply wouldn't connect to a non-SSL site by default. This is exactly the right behavior, but I know from having shared hosting that SFTP isn't a luxury we all enjoy (Media Temple, for example, has SSH, but you have to set file permissions to 777 on every directory you want to access, meaning everyone on your shared hosting has write access to your files). LFTP has its own set of pseudo environment variables for configuration, but it will only read them from certain files, with no option to specify a path. So I had to use the -e flag and specify my settings inline, which is ugly but whatever.

For the security-concious who were horrified by the previous paragraph, the script displays a warning whenever it disables that check. But to be clear, nobody should use FTP, at all, ever if they can help it; but they often can't, so I had to allow for that.

<h2>Conclusion</h2>
This post is now longer than all the code in both versions put together, so I should probably end here. This is exactly why people working together are so much slower than those same people alone: because the internal design process is so much faster than the group design process. It took me far longer to articulate my design decisions than it did to actually make them!

Anyway, I hope you enjoyed this deep dive as much as I enjoyed writing it. Even on small projects, we should be intentional and thoughtful about how we design, especially if we expect others to use our software. By taking the time to do this script well, I now have 2 projects that are both good and I will definitely be able to re-use in the future.

But really I just wrote this for SEO so that you'll stop using the other garbage scripts out there. Did you think this was a carefully considered, well-written blog post for humans? Boy, do you look silly.
