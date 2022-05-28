perl API for mit.dk
===================

This is perl interface for mit.dk, Danish national email system 

Included a simple POP server for proxying mitdk for read-only mail access
and a simple downloader.

Installation
============

Unix/Linux
----------

* Install this module by opening command line and typing `cpan Net::MitDK` (with `sudo` if needed)

* Authenticate in NemID as written below

Windows
-------

* You'll need `perl`. Go to [strawberry perl](http://strawberryperl.com/) and fetch one.

* Install this module by opening command line and typing `cpan Net::MitDK`

* Authenticate in NemID as written below

* In the examples directory, run `perl win32-batch`. This will create `mitdk-renew.bat`
and `mitdk-server.bat`.  Put them into your windows Startup folder. Then run them.

* Set up your favourite desktop mail reader so it connects to a POP3 server
running on server localhost, port 8111. Username is 'default', no password is needed.

* Optionally, if you want to forward the mails, you can choose from numerous
programs that can forward mails from a POP3 server to another mail account
[(list of
examples)](https://blogs.technet.microsoft.com/brucecowper/2005/03/18/pop-connectors-pullers-for-exchange/).
If you use Outlook it [can do that
too](https://www.laptopmag.com/articles/how-to-set-up-auto-forwarding-in-outlook-2013).

Upgrading
---------

* Stop `mitdk2pop` and `mitdk-renew-lease` if running.

* Install the dev version from github. Download/clone the repo, then run

```
  perl Makefile.PL
  make
  make install
```
(or `sudo make install`, depending); `gmake` instead of `make` for Windows.


One-time NemID registration
---------------------------

For each user, you will need to go through one-time registration through your
personal NemID signature. Run `mitdk-authenticate` to start a small webserver on
`http://localhost:9999/`, where you will need to connect to with a browser.
There, it will will try to show a standard NemID window. You will need to log
in there, in the way you usually do, using either one-time pads or the NemID
app, and then confirm the request from MitDK. If that works, the script will
create an authorization token and save it in your home catalog under
`.mitdk/default.profile`. This token will be used for password-less logins to
the MitDK site. In case it expires, in will need to be renewed using the same
procedure.

**Security note**:

Make sure that the content of .mitdk directory is only readable to you.

Lease renewal
-------------

MidDK only allows sessions for 20 minutes, then it requires a NemID relogin.
Therefore there is added a daemon, `mitdk-renew-lease`. You can run it from
cron (unix), or as a standalone program as `mitdk-renew-lease -la` (windows).
It then will renegotiate a lease every 10 minutes.

If for some reason the lease expires, it will warn you (once), but there's no
need to restart it after you made a successful relogin with
`mitdk-authenticate`.

Operations
==========

Download your mails as a mailbox
--------------------------------

Note: You probably don't need it, this script is mostly for testing that the access works.

On command line, type `mitdk-dump`, enter your passwords, and wait until it downloads
all into mitdk.mbox. Use your favourite mail agent to read it.

Use mit.dk as a POP3 server
-----------------------------

You may want this setup if you don't have a dedicated server, or don't want
to spam your mail by MitDK. You can run everything on a single desktop.

1) On command line, type `mitdk2pop`

2) Connect your mail client to POP3 server at localhost, where username is
'default' and password is empty string.

Use on mail server
------------------

This is the setup I use on my own remote server, where I connect to using
email clients to read my mail.

1) Create a startup script, f.ex. for FreeBSD see `example/mitdk2pop.freebsd`,
and for Debian/Ubuntu see `examples/mitdk2pop.debian`

2) Install *procmail* and *fetchmail*. Look into `example/procmailrc.local` and
and `examples/fetchmail` (the latter needs to have permissions 0600). 

3) Add a cron job f.ex.

`  2       2       *       *       *       /usr/local/bin/fetchmail > /dev/null 2>&1`

to fetch mails once a day. Only new mails will be fetched. This will also work for 
more than one user.

Automated forwarding
--------------------

You might want just to forward your MitDK messages to your mail address.  The
setup is basically same as in previous section, but see
`examples/procmailrc.forward.simple` instead.

The problem you might encounter is that the module generates mails as
originated from `noreply@mit.dk` and f.ex. Gmail won't accept that due to
[SPF](https://en.wikipedia.org/wiki/Sender_Policy_Framework). See if rewriting
the sender as in `examples/procmail.forward.srs` helps.

Enjoy!
