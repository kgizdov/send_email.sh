# send_email.sh (or SNDEM for short)
Script to send an email in bare-bone environments with minimal requirements

Depends only on:
 - coreutils (`base64`, `numfmt`, `stat`)
 - bash
 - curl
Aimed at sending emails from within
continuous integration runners
and other feature-limited light
environments

It's flexible and supports supplying information
through environment variable (useful in CIs) and
on the command line.

```bash
$ ./send_email -s 'smtp://smtp.gmail.com:587' -t 'username:password' -a 'my@email.com' -r 'your@email.com,teamgroup@email.com' -R 'Your Name,Team Name'
```
**OR**
```bash
$ export SNDEM_EMAIL_SERVER='smtp://smtp.gmail.com:587'
$ export SNDEM_EMAIL_AUTH='username:password'
$ export SNDEM_FROM_EMAIL='my@email.com'
$ export SNDEM_RCPNT_EMAILS='your@email.com,teamgroup@email.com'
$ export SNDEM_RCPNT_NAMES='Your Name,Team Name'
$ ./send_email
```
It has another feature to also accept either raw or `base64` encoded strings
for its input arguments/variables. E.g.
```bash
# it can run like this
$ ./send_email -s 'smtp://smtp.gmail.com:587' -t 'username:password' -a 'my@email.com' -r 'your@email.com'
# or like this
$ ./send_email -s 'c210cDovL3NtdHAuZ21haWwuY29tOjU4Nwo=' -t 'dXNlcm5hbWU6cGFzc3dvcmQK' -a 'bXlAZW1haWwuY29tCg==' -r 'eW91ckBlbWFpbC5jb20K'
```
Moreover, you can mix and match as needed.
```bash
# like this
$ ./send_email -s 'smtp://smtp.gmail.com:587' -t 'dXNlcm5hbWU6cGFzc3dvcmQK' -a 'my@email.com' -r 'eW91ckBlbWFpbC5jb20K'
# or even like this
$ env SNDEM_EMAIL_AUTH='dXNlcm5hbWU6cGFzc3dvcmQK' ./send_email -s 'smtp://smtp.gmail.com:587' -a 'bXlAZW1haWwuY29tCg==' -r 'eW91ckBlbWFpbC5jb20K'
```
The last one is especially useful as some CI environments only allow masked variables
if they are `base64` encoded strings. This way, you can easily hide your email credentials from logs.

You can also mix and match and include things like your commit message or attachments or both
```bash
$ env SNDEM_EMAIL_SERVER='smtp://smtp.gmail.com:587' SNDEM_EMAIL_AUTH='dXNlcm5hbWU6cGFzc3dvcmQK' \
      SNDEM_FROM_EMAIL='my@email.com' \
      SNDEM_RCPNT_EMAILS='someones@email.com' \
      SNDEM_RCPNT_NAMES='Some One' \
      SNDEM_CI_COMMIT_MESSAGE='My commit message' \
      ./send_email -M \
        -b 'Hey,\na new CI build completed for the commit: __CMTMSG_POS__\nHave a look.\nRegards\nCI Runner' \
        -s 'Awesome Subject' \
        -c 'cc@cc.com,ccc@cc.com' \
        -b 'bcc@bcc.com' \
        -A artifacts.zip
```
The above shows a custom email body with a positional argument for where the commit message is
to be placed. The resulting email will look like:
```
Hey,
a new CI build completed for the commit: My commit message
Have a look.
Regards
CI Runner
```
and `artifacts.zip` will be attached to the email

For more options run `send_email -h`.
