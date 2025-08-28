# Invoice [![Build Status](https://travis-ci.org/nickjj/invoice.svg?branch=master)](http://travis-ci.org/nickjj/invoice)

A command line script that will help you calculate how much money you should
bill your clients every month.

It's a self contained Bash script with no dependencies other than a few
standard Unix tools.

*This repo hasn't gotten updated in years, is this project dead? Absolutely
not! I use it all the time and have processed hundreds upon hundreds of
invoices. It hasn't needed changing so far!*

## Demo Video

[![Demo video](https://nickjanetakis.com/assets/blog/cards/calculate-invoice-amounts-with-bash-as-a-freelance-developer-c23c8c564ab48b6f47b78a7aabea7c2eba7fd287eeeb3b3d1cfa7fcd9363183b.jpg)](https://nickjanetakis.com/blog/calculate-invoice-amounts-with-bash-as-a-freelance-developer)

The above video was recorded against v0.1.0 of this script. Things might have
changed since then, but you can treat this video as a high level overview of
what it looks like to use this script. The documentation will always be up to
date.

## Installation

All you have to do is download the `invoice` script, make sure it's executable
and place it somewhere on your system path.

#### 1 liner to get `invoice` downloaded to `/usr/local/bin`:

```sh
sudo curl \
  -L https://raw.githubusercontent.com/nickjj/invoice/v0.1.1/invoice \
  -o /usr/local/bin/invoice && sudo chmod +x /usr/local/bin/invoice
```

That will download the latest release. If you want to download the bleeding
edge version you can replace the version number with master to download it
from the master branch.

You can confirm it works by running `invoice`. You should be greeted with a few
usage examples.

## Examples

Let's say you wanted to see how much you should bill Acme Inc. for when it
comes to the work you did in March 2019 and your hourly rate is $200 for that
contract. Here's what you would run:

```
nick:~$ invoice acme/log.md 2019/03 200
$2000 10.00 $200 5 acme
```

You would be getting **$2,000** because you worked **10** hours at **$200**
/ hour and the **5** is how many days you worked that month.

#### acme/log.md

This is the path to your time sheet / log file. In order for this script to
work you will need to track your hours in a certain way, but it's extremely
flexible. We'll go over this in more detail later.

#### 2019/03

This is the time period you want to get information about. The format is
`YYYY/MM`, but you can also supply just `YYYY` to get yearly totals for
your own book keeping.

#### 200 (optional)

This is the amount to charge per hour. It defaults to 150 if you don't supply
it but you'll also be able to supply this value inside of a time sheet / log
file, so it's rare you'll ever have to supply it from the command line.

*You can see more examples by running `invoice` without any arguments*.

### DEBUG Mode

You can also optionally enable `DEBUG` mode by setting an environment variable:

```
nick:~$ DEBUG=1 invoice example/log.md 2019
 10.25   10.25
 300m    5.00
 1h 30m  1.50
 45m     0.75
$742.35 17.50 $42.42 4 example
```

The format and data contained in the last line of output is the same as the
previous example except there's different input but now you can also see the
numbers of hours worked for each day and how the human readable time tracking
information that you entered was converted into decimal hours.

This will help you manually double check the values if you were unsure of the
totals.

## Time Sheet / Log

In order for the above example to work, certain things need to exist in a
certain way.

If you're already doing contract work, chances are you're tracking your time
to some extent and are very likely jotting down the work you did during that
time. The format and ideas we're about to go over is coming from a lot of real
world experience.

### Best Practices Based on 20 Years of Doing Freelance Contract Work

- Put newest entries on top (it's easier to find them when you need them)
- Forget tracking things down to the second, 5-15 minute intervals is fine
- Try to account for user errors by making the format as flexible as possible
- When automating anything with money, triple check it manually until you're confident

### Example Log

Here's a real example from one of my contracts. This is a `log.md` file I keep
inside of a contract's folder on my computer (ie. `acme/log.md`). The only
things I changed were my hourly rate and the people / project names to keep it
anonymous.

```
# Time Sheet - 150

2018/02/12 | 1h 30m

- Fix /dev/null redirect bug (now errors get shown)
- Rename setup command to deploy in the foobar script
- Add FOOBAR_DEV with its own set of commands (just release for now)
- Get Docker / Docker Compose installed in the set up script and test it on DO
- Think about how to bootstrap the foobar script itself and auto-run a deploy

2018/02/09 | 5h

- Fix nginx.conf to avoid occasional DNS cache timeouts
- Create release command with all of the bells and whistles
- 90 minute call with Jim to go over the release command and fix a few issues

2018/02/06 | 1h

- Configure script to allow for using PWD as the root path (but you can override it via env var)
- Progress towards the new Bash script (working on the setup routine for production)

2018/02/05 | 30m

- Call with Jim, everything is looking good to move forward with Bash
```

The overall pattern here is I loosely track my time on a per day basis and I
write out short bullets for what I did during that time.

I don't sit there using stop watches while tracking things down to the exact
second. If I were to start working for today I would begin by writing
`2019/04/25 | Started at 8:30am` just so I have a note of when I began working.

Then, let's say I worked for 2 hours (as in it's 10:30am now). I would replace
`Started at 8:30am` with `2h` and that would be it. I would wrap things up by
writing out the bullets for what I did during those 2 hours.

#### File name and path

The file name must be `log.md` and it must exist inside of your contract's
folder.

For example, I keep all of my contract work in `/d/src/contracts`, so working
with the above example you would expect to have `acme/log.md` inside of that
folder for a complete path of `/d/src/contracts/acme/log.md`.

The file name is important because you can supply your contract directory to
the invoice script and it will calculate the amounts for all of your contracts.
In this case, it specifically looks for a `log.md` file in each contract
folder.

#### Custom hourly rate

If you plan to set a custom hourly rate for a contract, you must put `# Time
Sheet - 150` somewhere in the file.

The `150` is the hourly (which you can change) and the `Time Sheet` is
something this script searches for (which you can't change). I recommend
putting it at the very top of the file just so it's easy to find.

#### Date formatting

The date must be at the start of the line.

Technically you can format it however you want as long as what you pass into
the invoice script at run-time matches what you have in your log. The only
exception is you can't separate your YYYY MM DD with `|` since that's used
elsewhere.

I recommend going with `YYYY/MM/DD` because it's a well known format, easy to
type and it makes it easier to search by year or by year + month since the
largest time interval is on the left. Now grepping for `2019` or `2019/04` is
easy.

#### Time tracking

All of the following formats are valid and result in the same value of 1 hour
and 30 minutes:

- 1.5
- 1:30
- 1h 30m
- 90m

The time must be at the end of the same line that has the date and you must
separate them with a single `|`.

The script is flexible when it comes to spacing. I recommend putting 1 space
between the `|` on each side (like the example) since it's easy to read but it
will work if you forget a space or add more than 1 adjacent space.

Overall I recommend you stick with the same date formatting and time tracking
formats for all of your logs just to keep it readable but the script will work
if you mix and match formats. You can see examples of that in the [tests
directory](https://github.com/nickjj/invoice/tree/master/tests) of this repo.

#### Bullet notes

These aren't required. I add them because when I invoice my clients every month
I tend to put a high level overview of what was worked on. Having these lower
level bullets written out lets me come up with the invoice details very
quickly.

## Testing

Despite freelancing for such a long time I've only developed and started using
this script in 2019.

Although for many many years I've logged all of my work exactly how you saw in
the example log file. I found it the easiest to manually parse, and it just so
happens it's pretty easy to parse with code too.

I manually went through an entire year's worth of invoices I've sent to clients
and triple checked every single invoice to the result of this script and in
every single case the results matched up to the cent.

There's also automated tests that cover 20+ different time tracking formats
which all work, as well as end to end tests on some example test logs. All of
this is available to see in the CI build history.

Still, even with all of that, before you pull the trigger and bill your clients
you should calculate your hours manually just to make sure it matches. I
don't do that anymore since I have total confidence in this script, but you
should to begin with just to be safe!

## About the Author

I'm a self taught developer and have been freelancing for the last ~20 years.
You can read about everything I've learned along the way on my site at
[https://nickjanetakis.com](https://nickjanetakis.com/). There's hundreds of
blog posts and some video courses.
