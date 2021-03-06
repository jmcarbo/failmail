# Failmail

Failmail is an SMTP proxy that receives emails, collecting the emails it gets
within a configurable interval into summary emails, and sends the summary
emails through another SMTP server. Its primary design goals are:

1. to help both human operators of e.g. a web application or system under
   observation to understand the errors and notifications that come from such a
   system
2. to prevent an upstream SMTP server from throttling or dropping messages
   under a high volume of errors/alerts
3. to be easier to interoperate with than its predecessor,
   [failnozzle](http://github.com/wingu/failnozzle), which had strict
   assumptions about the nature and format of incoming messages


## Installation

    $ export GOPATH=...
    $ go get -u github.com/hut8labs/failmail
    $ $GOPATH/bin/failmail


## Usage

    $ failmail --help
    Usage of ./failmail:
      --all-dir="": write all sends to this maildir
      --bind="localhost:2525": local bind address
      --fail-dir="failed": write failed sends to this maildir
      --from="failmail@myhostname": from address
      --max-wait=5m0s: wait at most this long from first message to send summary
      --relay="localhost:25": relay server address
      --wait=30s: wait this long for more batchable messages

So by default, `failmail` listens on the local port 2525
(`--bind="localhost:2525"`), and relays mail to another SMTP server (e.g.
Postfix) running on the local default SMTP port (`--relay="localhost:25"`). It
receives messages and rolls them into summaries based on their subjects,
sending a summary email out 30 seconds (`--wait=30s`) after it stops receiving
messages with those subjects, delaying no more than a total of 5 minutes
(`--wait=5m`).

Any summary emails that it can't send via the server on port 25, it writes to a
maildir (`--fail-dir="failed"`; readable by e.g. `mutt`, or any text editor).
If the `--all-dir` option is given, `failmail` will write any email it gets to
a maildir for inspection, debugging, or archival.

## Todo

Work in progress, ideas good and bad, and otherwise:

* exposing Failmail's mechanism for grouping emails/splitting emails among
  summary emails on the command line
* an HTTP or other interface to stats about received mails (e.g. for
  monitoring)
* rate monitoring (a failnozzle feature): send an email to e.g. a pager if the
  number of incoming emails excceeds some limit
* shell script hooks: run a shell command after sending a summary email
* ...
