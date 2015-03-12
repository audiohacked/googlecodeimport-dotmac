_a step by step guide for successfully building your .mac replacement._

# Introduction #

This document details all steps to take for successfully building your .mac replacement.


# The staircase #

## Step 1 ##
#### Set up your server ####

Setup a basic Linux/Unix server; configure hostname and ip-address. Install Perl.
Either install a build environment, or fetch precompiled packages for Apache httpd here (available shortly).
In case you decided to compile your own Apache httpd server, fetch the source code and the (Patches) quota patch (from this site), patch, configure, compile.
Install Apache httpd.

Any OS specific notes can be found [here](http://code.google.com/p/dotmac/wiki/OS_specific_notes)
## Step 2 ##
#### Configure your webserver ####

Fetch the code/folders (here) from svn.
All code (/server) assumes, it'll end up in /var/www/dotmac/
This'll guarantee a minimal configuration effort.

A special .conf file was provided (here) in addition to your standard httpd.conf.
This file ((Configuration) dotmac.conf) _must_ be edited.
Especially the ip-address for your ssl host needs to be specified.
In most linux distros it should end up in /etc/httpd/conf.d or /etc/apache2/conf.d

## Step 3 ##
#### Set up you scripts ####

mod\_perl needs to be installed with your webserver. mod\_perl is available as package for most
popular linux distributions.
  * All folders _except_ ./dotmac/private should be owned by the user/group apache runs as.
(nobody, apache, www-data)
  * ./dotmac/private contains both server certificates and the .htdigest (user/password) databases for admins and users.
  * ./dotmac/private/iDiskUsers should be writable by the user/group apache runs as.

The perlmodules have a few dependencies; they're easily installed from CPAN:
```
perl -MCPAN -e 'install HTTPD::UserAdmin'
perl -MCPAN -e 'install MD5'
perl -MCPAN -e 'install XML::DOM'
perl -MCPAN -e 'install HTTP::DAV'
perl -MCPAN -e 'install XML::LibXML'
```
_Note: For XML::LibXML to compile, you will need to make sure that the development package for libxml2 in installed_
## Step 4 ##
#### Setting up hosts on your client machine(s) ####
```
www.mac.com
syncmgmt.mac.com
idisk.mac.com
configuration.apple.com
lcs.mac.com
certinfo.mac.com
delta.mac.com
notify.mac.com
publish.mac.com
homepage.mac.com
```
should all point to your server. You can either do this by editing /etc/hosts on your client machine(s);
```
###.###.###.### www.mac.com syncmgmt.mac.com idisk.mac.com configuration.apple.com lcs.mac.com certinfo.mac.com delta.mac.com notify.mac.com certinfo.me.com publish.mac.com homepage.mac.com
###.###.###.### www.mac.com. syncmgmt.mac.com. idisk.mac.com. configuration.apple.com. lcs.mac.com. certinfo.mac.com. delta.mac.com. notify.mac.com. publish.mac.com. homepage.mac.com. certinfo.me.com.
```

or by setting up your own DNS server.
## Step 5 ##
#### Create your first user(s) ####

A skeleton is provided for new users; create /var/www/dotmac/username, and copy everything in /var/www/dotmac/skel/ to /var/www/dotmac/idisk/username/

for testing purposes, a test user has been provided: l/p testuser/dotmac

dotmac.conf has been completely re-written in perl. It automatically creates (.conf) locations for users, with quota specified.

This requires the use of idiskAdmin; http://configuration.apple.com/idiskAdmin

idiskAdmin is password protected (l/p admin/dotmac); users should change these credentials asap.
```
htdigest /var/www/dotmac/private/iDiskAdmins idisk.mac.com admin
```
For now, idiskAdmin just sets/edits quota and username/password; the skeleton still needs to be copied by hand. New users/changes will be reflected upon server restart (graceful).


## Step 6 ##
#### Check(s) ####

By now you should be able to k (from the finder on a client machine) to http://idisk.mac.com/username
(if not, try looking at your apache error logs, and adapt your configuration)

_this is to be repeated for all (new) clients:_
Point your (safari) browser to https://idisk.mac.com/username
You will be challenged with an untrusted certificate - actually this is the certificate you _do_ want to trust.
Examine the certificate, and drag the icon of the certificate to your Desktop (or any folder of your choice).
Open /Applications/Utilities/Keychain Access, and (if necessary, tick the button 'Show Keychains' at bottom-left)
By now you should see a menu 'Keychains' in the top-left. Tick the 'login' entry. Drag the certificate (from whereever you put it when dragging it from your browser) in the pane where all other certificates reside. Locate it (in the pane) and double-click it. Change the trust settings to 'always trust'.
After Logout/Login - check (pointing your browser https://idisk.mac.com/username) if the certificate is trusted.
If so... you're done!

Enjoy the magic, when choosing your .mac preferences pane from your system preferences !


## Looking over the edge ##
#### Current status ####

10.4 "Tiger"
  * .Mac prefpane
  * iSync support
  * iDisk
  * iDisk sync
  * Backup

10.5 "Leopard"
  * .Mac prefpane
  * iSync
  * iDisk
  * iDisk sync
  * Backup

**(_note - currently there's no interoperability between 10.4 and 10.5 sync clients)_**

#### Operating Systems Verified ####

Users reported the following Operating Systems Working:
  * CentOS 5
  * Fedora Core 5, 6, ..., 10
  * Debian 'etch'
  * Ubuntu 'Edgy Eft' (6.x), 'Feisty Fawn' (7.x), 'Hardy Heron' (8.x)
  * freeBSD
  * Solaris 10
  * Leopard Server
  * NSLU2 (with some tweaks)

please continue reporting!

#### Updates for the binary release (0.5) ####
some files were edited after the binary release - so these have to be updated by hand:
  * http://dotmac.googlecode.com/svn/trunk/server/var/www/dotmac/perlmodules/DotMac/UserFolderAuthz.pm should end up in /var/www/dotmac/perlmodules (replacing the old one)
  * from http://code.google.com/p/dotmac/source/browse/#svn/trunk/server/var/www/dotmac/configuration/configurations/internetservices/syncservices clientConfiguration-10.5.n should end up in /var/www/dotmac/configuration/configurations/internetservices/syncservices/ Users have reported these files are all similar; so copying clientConfiguration-10.5.2 to clientConfiguration-10.5.3...clientConfiguration-10.5.10 works as well.


#### Global Hints ####

  * A common pitfall seems to be the SSL host. [Due](http://httpd.apache.org/docs/2.2/ssl/ssl_faq.html#vhosts2) to the nature of Apache HTTPd SSL hosts, it's not possible to use Name-Based Virtual Hosting to identify different SSL hosts. Some linux distros configure a SSL host upon installing Apache; this host needs to be deleted for enabling ours. If you need more SSL hosts; you either need to use separate IP addresses for different SSL hosts, or (DNS) alias the new hosts within ours. (In the latter case, edit ./dotmac/private/extensions to your needs, and use signKey.sh to create a new certificate).

  * Our virtual hosts on port :80 are Name Based Virtual Hosts. If you need to specify more, you might want to have a glance at the [Apache Manual](http://httpd.apache.org/docs/2.2/vhosts/name-based.html#using).

  * Perl modules are only compiled (once) at server startup. When updating perlmodules over svn, make sure issuing a (apachectl) graceful/restart.

  * **_Do_** use the [Issues](http://code.google.com/p/dotmac/issues/list) tab; both users and developers are helping each-other out there.

  * The certificate we ship has expired. You can create a new certificate by running the script 'signKey.sh' in /var/www/dotmac/private (don't forget to restart apache afterwards). Running this script will enable all dotmac hosts we use (from the file 'extensions'). Good practice would be creating your own certificate of course (and using signKey for enabling the dns hosts from 'extensions').

#### Continue Reading ####
  * [Help the project](http://code.google.com/p/dotmac/wiki/Help_the_project)
  * [Plans](http://code.google.com/p/dotmac/wiki/Plans)
  * [The iLife branch](http://code.google.com/p/dotmac/wiki/iLife). When you feel familiar with your setup, you might want to consider moving to the iLife branch straightaway!