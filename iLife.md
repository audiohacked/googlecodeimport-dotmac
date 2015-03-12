## instructions on building from the iLife branch ##

# Introduction #

The developers have been using/developing on the iLife branch for over half a year now.
We think it's about time to push things towards stable.
We invite the daredevils amongst [you](http://dotmac.googlecode.com/svn/wiki/images/plotgraph.png) to try installing from the iLife branch - and give us feedback.

## Supported Features ##
#### Current status ####

10.5 "Leopard"
  * .Mac prefpane
  * iSync
  * iDisk
  * iDisk sync
  * Backup
  * iPhoto '09 gallery publishing
  * iWeb '09 publishing

**(_note - the iLife branch dropped support for os 10.4. As iLife 09 was introduced, our main focus moved there. Don't expect us to backport our changes to iLife 08)_**

# Install #

Install is globally the same as from trunk.

We moved to a db backend for auth. This means we got rid of the .htdigest files.
You'll need to have (a patched) apache httpd >= 2.2 for being able to use the new backend.
_(on older apache versions; you'll still be able to install - you'll just need to keep the db in sync with your .htdigest
files. Consult the README.txt in ./private/obsolete for instructions)_

In your httpd.conf make sure you enable `mod_dbd.so` and `mod_authn_dbd.so`, you'll also need to make sure
you installed apr-util-sqlite.

Besides the perl modules needed for trunk we need a few more (available from CPAN):
  * LWP::UserAgent
  * JSON
  * Apache::Session::File
  * DateTime
  * DateTime::Format::HTTP
  * POSIX
  * Compress::Zlib
  * DBI
  * DBD::SQLite
  * Image::ExifTool
  * Imager

Before installing Imager, **you'll need to make sure to have jpeg headers installed**. On common linux distibutions these are packaged as libjpeg-devel.

On Mac OS Leopard:
> get the sources from [www.ijg.org](http://www.ijg.org/files/jpegsrc.v6b.tar.gz).
```
  tar zxvf jpegsrc.v6b.tar.gz
  cd jpeg-6b/
  ./configure
  make

  sudo mkdir /usr/local/include
  sudo mkdir /usr/local/lib

  sudo make install-lib
```
> after that you can install Imager with: `sudo perl -MCPAN -e 'install Imager'`

(the list might not be complete - please let us know when you find missing modules)

Also make sure you enable mod\_rewrite in your Apache config

The hosts you'll need to enter in /etc/hosts on your client machines:
```
###.###.###.###  www.mac.com web.me.com syncmgmt.mac.com idisk.mac.com idisk.me.com configuration.apple.com lcs.mac.com certinfo.mac.com delta.mac.com notify.mac.com publish.mac.com publish.me.com homepage.mac.com webservices.mac.com web.mac.com gallery.mac.com photocast.mac.com m3.mac.com members.mac.com
###.###.###.### www.mac.com. web.me.com. syncmgmt.mac.com. idisk.mac.com. idisk.me.com. configuration.apple.com. lcs.mac.com. certinfo.mac.com. delta.mac.com. notify.mac.com. publish.mac.com. publish.me.com. homepage.mac.com. webservices.mac.com. web.mac.com. gallery.mac.com. photocast.mac.com. m3.mac.com. members.mac.com.

```

## Gallery ##
Getting gallery.yourdomain.com working requires you (besides configuring it in dotmac.conf) to replace the hardcoded 'yourdomain.com' some times.
  * ../cache/gallery.html (1x)
  * ../cache/g/javascripts/gallery.js (3x)
  * when yourdomain is not a `.com` domain - you also have to fix the extension in gallery.js. To do this, search for `.com` in gallery.js; it should be your 4th hit (at line #162 - saying: `return path.replace(/http:\/\/.+?\.com\/.+?\//,'');)`; replace `.com` with your extension. _we're looking for a more permanent solution_ Can people please verify if replacing the whole statement with `return path.replace(/http:\/\/.+?\.[a-zA-Z]{2,3}\/.+?\//,'');` works for them - and give feedback.


We've opened a [new issue](http://code.google.com/p/dotmac/issues/detail?id=88) where we hope to gather your experiences/issues. Please help us out making this page complete.


# Notes #

  * iPhoto and iWeb publishing have only been tested with iLife '08 with latest updates. We won't support earlier versions/revisions.
  * On some older Apache versions the ./private dir should be writable by Apache