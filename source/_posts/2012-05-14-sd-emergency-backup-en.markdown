---
title: SD-Card emergency encrypted backup
date: 2012-05-14 14:49
category: linux
tags: ecryptfs, backup, ext4, sdcard
lang: en
---

![](./img/backup-logo.jpeg)

I must confess I am a little bit obsessive about data loss. I am not just obsessed, but *paranoid*.
I like backups. I love them. I do it constantly, automatically, distributed and encrypted. I even
[write my own backup tools][backup]. It's never enough.

I usually have a `500GB` 2.5" hard disk connected to my laptop when I'm working at home, and use
`fcron` and my `backup.py` to take a complete snapshot of the working environment every 5h or so. I
also *backup my backups* on another hard disk, on another city, each time I pass by my family
house. I use *Dropbox* too. I love *Dropbox*. But I do not trust it, I don't rely on it. I want to
**keep my data** myself, so I replicate the 'Dropbox directory' at my own home server, with regular
backups of it, and at my family's PC (with its own backup schema as well). Sensible data is
encrypted by using `ecryptfs` because this way, data happens to be scattered across different
places and although barely accessible, it wouldn't take much to a geek with physical access to
hardware to get it.

But as I said before, it is never enough. This way of making backups isn't bad, It makes it
difficult to completely lose *everything*. But what happens with last-time files and changes?. I
often imagine an scenario (a bad nightmare, really) where I'm travelling, carrying all my stuff
with me, and for some stupid reason, my bag falls to the road being swiftly smashed and crunched by
an evil double-decker. There is not hard-disk anymore, either the laptop's or the backup one. They're
magnetics flying fragments now. *Dropbox* happened to be unsynced since a day or so of no internet
connection, my last backup at home is 3 days old, my sister accidentally removed some backups to
make room for a movie and I was being quite productive these past blank hours at the airport.
**Data loss**. Horreur.  I wake up screaming, soaked in cold sweat.

I know it's not very wise to move around with data and backups in the same bag, and actually I
don't. And I also know that the chance for absolutely everything to fail it's quite low, but you got
the point, and remember: I'm *paranoid*.

Then I was at a store and saw one of those 32Gb tiny flash pen-drives. 32Gb, that's a lot. At least for
my important data being about 7Gb. Then I thought I could regularly backup my physical disk
backups, not only after some weeks, but every few days, into a pen I'll always carry around
inserted in my keyring, performing the copies manually.

But after the first moment, I didn't like the idea. Besides of being a backup-freak, I am, as any
other conceited programmer, quite lazy about repetitive tasks. Even further, I'm slack. If there is
any task that I must perform completely unchanged more than 10 times, I'll start growing annoyed
about the 11th time. Actually I'm being self-indulgent here, let's say 5 times. If I have to
remember and manually perform each backup, I will screw it at some copy, forget about it, delay
doing them or a combination of the previous. I wanted my flash copies, but I wanted them
*automated*.

Next thing I saw was a *32Gb sd-card*. They're unlikely to die in an accident, they even survive
being washed along clothes. They have this cell [worn out][flash] issue, but hey, they're also relatively
cheap. It would be nice to have one of those inserted into my machine and perform copies to it. My
laptop happens to have one of those integrated card-readers, so I just went straight to *Amazon* and
ordered a [*class-10 32Gb* for about £17][amazon-card].

![Laptop integrated SD card-reader](./img/card-reader.jpg)

This way I could have an absolutely up-to-date copy of all my data with me, automatically done at
each system shutdown or every few hours. It won't help me in case of losing my laptop, because it
would be gone with it, but it can definitely save my butt if some drastic hardware failure happens.
There are a couple of drawbacks for backing up to a SD card:

1. Flash cells worn out on write. If you constantly write to a flash memory, you will exhaust it
soon.
2. It is exposed. The **** card suddenly pops out when careless manipulating the laptop. Or someone could
take it. It may be lost somewhere, with all my data in it.

Fortunately, (1) incremental backups only writes few Mb each time, so it won't be so bad for cells,
and (2) encryption exists for a reason, I can always prevent all it's contents from being taken.

Once I got my card, I moved it from `vfat` (it has issues with +4Gb files), to `ext4` and created a
`ecryptfs` private directory on it.

Once the sdcard has been inserted, it is auto mounted by gnome on `/media/4785B20834`, so let's get
the device number from the mountpoint.

	$$ df -h /media/4785B20834 
	Filesystem            Size  Used Avail Use% Mounted on
	/dev/sdb1              30G   44M   28G   1% /media/4785B20834

Once we know it's `/dev/sdb1`, (1) unmount it and (2) format it. I will format
it using `ext4` with no [journaling][]. Journaling helps to keep data integrity, by
keeping an index of last recently written files, but it means constantly write
a list on the device. We don't want that, remember?

	$$ eject /media/4785B20834
	# -O ^has_journal disables journaling
	# -L sets the volume label
	$$ sudo mkfs.ext4 -O ^has_journal /dev/sdb1 -L sdbk

	mke2fs 1.41.12 (17-May-2010)
	Filesystem label=sdbk
	OS type: Linux
	Block size=4096 (log=2)
	Fragment size=4096 (log=2)
	Stride=0 blocks, Stripe width=0 blocks
	1957888 inodes, 7817984 blocks
	390899 blocks (5.00%) reserved for the super user
	First data block=0
	Maximum filesystem blocks=4294967296
	239 block groups
	32768 blocks per group, 32768 fragments per group
	8192 inodes per group
	Superblock backups stored on blocks: 
	32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
	4096000

	Writing inode tables: dataone                            
	Writing superblocks and filesystem accounting information: 
	done

	$$ udisks --mount /dev/sdb1
	Mounted /org/freedesktop/UDisks/devices/sdb1 at /media/sdbk

Well, we already have the card, let's go for the encryption. `ecryptfs` allows
you to encrypt a certain directory on the fly, storing the data encrypted, and
mounting it on at some point on '*clear*', so you can work with it normally,
while still ciphering file contents underneath one by one and changing its
names. `ecryptfs` can be found in the `ecryptfs-utils` package.

So let's go for it, first of all (1) create the `ecrypt` directory by mounting
it for the first time, (2) answer the questions that the program raises: I use
the `aes` algorithm, with a `32 bytes` key, no to `plaintext passthrough` and
yes to `filename encryption`.  You can see the settings for your new partition
when it is created, and (3) take a look to your password's signature
(`8e85340a483cb3ce`) because you'll recognize it in further steps.

		mkdir /media/sdbk/bk
		sudo mount -t ecryptfs /media/sdbk/bk /media/sdbk/bk

		Passphrase: 
		Select cipher: 
	->  1) aes: blocksize = 16; min keysize = 16; max keysize = 32 (not loaded)
		2) blowfish: blocksize = 16; min keysize = 16; max keysize = 56 (not loaded)
		3) des3_ede: blocksize = 8; min keysize = 24; max keysize = 24 (not loaded)
		4) twofish: blocksize = 16; min keysize = 16; max keysize = 32 (not loaded)
		5) cast6: blocksize = 16; min keysize = 16; max keysize = 32 (not loaded)
		6) cast5: blocksize = 8; min keysize = 5; max keysize = 16 (not loaded)
		Selection [aes]: 1
		Select key bytes: 
		1) 16
    ->	2) 32
		3) 24
		Selection [16]: 2
		Enable plaintext passthrough (y/n) [n]: n    <- Nope
		Enable filename encryption (y/n) [n]: y      <- Yep
		Filename Encryption Key (FNEK) Signature [8e85340a483cb3ce]:  
		Attempting to mount with the following options:   ^-- Look!
		ecryptfs_unlink_sigs
		ecryptfs_fnek_sig=8e85340a483cb3ce
		ecryptfs_key_bytes=32
		ecryptfs_cipher=aes
		ecryptfs_sig=8e85340a483cb3ce
		Mounted eCryptfs

Once you have it mounted, let's put some sensible data in it, unmount it and see what happens.

	# ex-girlfriend pic
	cp ~/Photos/DSC0069.jpg /media/sdbk/bk
	ls /media/sdbk/bk
	DSC0069.jpg

	# umount it
	sudo umount /media/sdbk/bk
	ls /media/sdbk/bk
	ECRYPTFS_FNEK_ENCRYPTED.FWaCVHE8G1mnnUZhhyZe.Z6vRrz42cu8a5gt8p.H7pcoIB8nbl3brC-QfE--

Oh! so when we do not set the password and mount the directory, all we have in
there are weird files. Just perfect. If you mount it again with `mount -t
ecryptfs DIR DIR`, you'll have to go through the whole set-up process again and
answer algorithms, sizes and options to be able to mount. Second step is to
make it mount automatically without having to type our password. 

We'll use the `gnome-keyring` for this. The `gnome-keyring` will pop out at
system start-up asking for the keyring password, and once you have supplied it,
applications can request passwords by its signature (ahh! `8e85340a483cb3ce`)
and use it without user interaction.

First, we'll (1) add the partition to `/etc/fstab`, so it will mount
automatically and configure it so the user can mount it without being root.
Once we've got that, we (2) add our password to the `gnome-keyring` and (3)
enjoy mounting/unmounting as we please.

	# 1. Mount it and get the config from /etc/mtab
	mount -t ecryptfs /media/sdbk/bk /media/sdbk/bk
	Passphrase: 
	Select cipher: 
	1) aes: blocksize = 16; min keysize = 16; max keysize = 32 (not loaded)
	2) blowfish: blocksize = 16; min keysize = 16; max keysize = 56 (not loaded)

	(... yes, all this again ...)
	Mounted eCryptfs
	$$ ls /media/sdbk/bk
	DSC0069.jpg
    $$ grep ecryptfs /etc/mtab
	/media/sdbk/bk /media/sdbk/bk ecryptfs rw,ecryptfs_sig=8e85340a483cb3ce,ecryptfs_cipher=aes,
			ecryptfs_key_bytes=32,ecryptfs_fnek_sig=8e85340a483cb3ce,ecryptfs_unlink_sigs 0 0
	$$ sudo vim /etc/fstab

We add `user` to the options, so the user can `mount`/`umount` as he wishes. Leaving the file like this

	# sdbk
	/media/sdbk/bk /media/sdbk/bk ecryptfs user,noauto,rw,ecryptfs_sig=8e85340a483cb3ce,ecryptfs_cipher=aes,
			ecryptfs_key_bytes=32,ecryptfs_fnek_sig=8e85340a483cb3ce,ecryptfs_unlink_sigs 0 0

Well, we have it on the `/etc/fstab`, ready to mount at startup and at anytime
we do `mount -i`. Lets add the key to the keyring to avoid typing it each time.

	# 2. add key to the keyring
	$$ ecryptfs-manager
	eCryptfs key management menu
	-------------------------------
		1. Add passphrase key to keyring  <--- this one
		2. Add public key to keyring
		3. Generate new public/private keypair
		4. Exit

	Make selection: 1

		Mount-wide passphrase:   <-- type your password
		Confirm passphrase:      <-- do it again
		Using the default salt value      
                                          v---- Look ma! same signature!
	Added key to keyring with signature [8e85340a483cb3ce].

Now it's time to test that everything works as expected. Lets (1) unmount it
and then (2) mount it with no password annoyance, and finally (3) unmount it
the same way.

	# umount it as root
	$$ sudo umount /media/sdbk/bk
	# check whether we can mount it
	$$ mount -i /media/sdbk/bk
	$$ ls /media/sdbk/bk
	DSC0069.jpg
	$$ umount /media/sdbk/bk

Got it!  This way is easy enough to get encrypted contents in the sdcard. All
we have to do now it's automate the backups via `fcron`. I use `fcron` and not
just `cron` because its for a laptop. `fcron` works similar to `anacron` and
remembers where it was at system shutdown. So if I set it to make a backup each
5h, work for 2 and then shut it down, next time it will keep the count and wait
just 3h for the next backup.

	Options frequency command
	2 # if lavg5 <= 0.5 wait 1h max, start inmediately if it had to happen whilst off
	3 @lavg5(0.5),until(1h),bootrun(true) 5h python2.6 backup.py --plan .hdbk.conf backup
	4 @lavg5(0.5),until(1h),bootrun(true) 4h python2.6 backup.py --plan .sdbk.conf backup

I have it one each 5h and another each 4h. Starting only when system average load is less than 0.5.
Backup!

**EDIT**: I've found that this configuration does not properly mount the `ecryptfs` file system on
startup, and trying it with `sudo mount -a` it asks for the password. I'm obviously missing
something. My solution was to add `noauto` to the `fstab` options so the partition won't be mounted
on startup and to add `mount -i /media/sdbk/bk` in my `.profile` file this way.

	# Mount ecryptfs sdbk directory
	SDBK="/mnt/sdbk/bk"
	if [ -d "$SDBK" ]; then
		mount -i "$SDBK"
	fi


[backup]: http://github.com/jvrsantacruz/scripts/tree/master/backup
[ecryptfs-README]: http://ecryptfs.sourceforge.net/README
[ecryptfs-blog]: http://tombuntu.com/index.php/2008/08/07/create-an-encrypted-private-directory-with-ecryptfs/
[ext4]: http://en.wikipedia.org/wiki/Ext4
[journaling]: http://en.wikipedia.org/wiki/Journaling_file_system
[amazon-card]: http://www.amazon.co.uk/s?ie=UTF8&tag=firefox-uk-21&index=blended&link_code=qs&field-keywords=class-10%20card&sourceid=Mozilla-search
[flash]: http://en.wikipedia.org/wiki/Flash_memory

