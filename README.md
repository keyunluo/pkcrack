# PkCrack - Breaking PkZip-encryption

## Table of Contents
---

- [Introduction](#introduction)
- [Build](#build)
- [Usage](#usage)
- [More](#more)
- [Demo](#demo)
- [Details](#details)
---

## Introduction

This package implements an algorithm that was developed by Eli Biham and Paul Kocher and that is described in this [paper (Postscript, 80k)](ftp://utopia.hacktic.nl/pub/crypto/cracking/pkzip.ps.gz).

The attack is a `known plaintext attack`, which means you have to know part of the encrypted data in order to break the cipher.

The `original` file is [here](https://www.unix-ag.uni-kl.de/~conrad/krypto/pkcrack/pkcrack-1.2.2.tar.gz), and the official website is https://www.unix-ag.uni-kl.de/~conrad/krypto/pkcrack.html.

This repositoriy is reconstructed with the modern build tool  `CMAKE`.

## Build

For Linux/Mac

``` shell
git clone https://github.com/keyunluo/pkcrack
mkdir pkcrack/build
cd pkcrack/build
cmake ..
make
```

If you are using `Windows`, after `cmake` step you should open Visual Studio for building in the build directory.

## Usage

You need two files:
- the ZIP-archive which you want decrypted, and
- another ZIP-archive, containing at least one of the files from the encrypted archive in `unencrypted` form. This one has to be compressed with the same compression method used for the encrypted file.

Now, enter
``` 
  pkcrack -C encrypted-ZIP -c ciphertextname -P plaintext-ZIP 
          -p plaintextname -d decrypted_file -a
```

(This is supposed to be a single line. I've wrapped it because it doesn't fit in 80 chars. "Real computer scientists never comment their code - the identifiers are so long they can't afford the disc space." :-/ )

- encrypted-ZIP: is the name (and path) of the encrypted ZIP-archive (see 1. above)
- ciphertextname: is the name of the file in the archive, for which you have the - plaintext: 
- plaintext-ZIP: is the name (and path) of the ZIP-archive containing the compressed plaintext (see 2. above)
- plaintextname: is the name of the file in the archive containing the known plaintext
- decrypted_file: is the name of a file to which the decrypted archive will be written

All you have to do now is wait a little. Depending on the size of the plaintext and the speed of your computer after about an hour the program should terminate. If the plaintext is very short (less than 100 bytes or so), it will probably take a lot longer.

After pkcrack is finished, you will find the decrypted archive in the file decrypted_file. You can unzip it using any unzip-program, e. g. pkunzip under DOS, or unzip under UNIX.

If `decrypted_file` doesn't exist, or if unzipping it produces CRC-errors, there are several things that may have gone wrong:

- Maybe you used the wrong plaintext. Find better plaintext.
- Maybe you used the correct plaintext but used the wrong compression method. Compress the plaintext with a different method.
- Maybe pkcrack found more than one set of matching 'keys' and used the wrong one to decrypt the file. Examine the output of pkcrack, and use the sets of key[012]-values with the "zipdecrypt" program to decrypt the file.
- Maybe something else went wrong. Now you're in trouble. Read the "more complete instructions" and try to understand why the program failed. Read the FAQ below.

## More 

This section will explain some of the more esoteric options of pkcrack, as well as some of the other programs in this package.

Just like in the "simple instructions" you need two files. Not necessarily ZIP-archives, though. You can specify any file containing nothing but plain data, i. e. no ZIP-headers, and no other fancy stuff. In that case, simply don't specify the -C or -P options, but only the -c and -p options.

So there are two possible ways to tell pkcrack where to find the ciphertext:

* -C encrypted-ZIP -c ciphertextname (see above), or
* -c ciphertextname

As I said, in the latter case ciphertextname is the name of a file containing `nothing but` encrypted data.

Analogously there are two possible ways to specify the plaintext:

* -P plaintext-ZIP -p plaintextname (see above), or
* -p plaintextname

In the latter case, plaintextname is the name of a file containing `nothing but`(compressed) plaintext.

Note that PkZip prepends 12 random bytes to the compressed data before encryption, so the ciphertextfile has to be 12 bytes longer than the plaintext. If you know only part of the plaintext, the plaintext can be even shorter. Usually, though, a difference of more or less than 12 bytes in the file sizes indicates wrong plaintext, or wrong compression method of the plaintext.

If you want to extract data from a ZIP-archive, you can use the "extract" program contained in this package. Invoke it by entering

``` sh
  extract ZIP-name name-in-ZIP
```

This will extract the (possibly encrypted and/or compressed) data stored in the archive *ZIP-name* under the name *name-in-ZIP*, and write those data to the file *name-in-ZIP* in the current directory. If *name-in-ZIP* contains the names of subdirectories you have to make sure these directories exist below the current directory before you start the extract program.

Another option used in the "simple instructions" above is the `-d decrypted_file` - option. It tells pkcrack to decrypt the archive specified with the -C option and write the decrypted results to the file decrypted_file. Naturally, it can only be used in conjunction with the -C option.

If you do not specify the -d option, pkcrack will try to find a PkZip-password when it has found a set of keys. If it finds a password, you can use it to decrypt the archive with the pkunzip-program. If it doesn't, you can use the set of keys found by pkcrack with the zipdecrypt program contained in this package to decrypt the archive. Zipdecrypt must be called as follows:
``` sh
  zipdecrypt key0 key1 key2 encrypted_archive decrypted_archive
```
where *key0, key1, key2* is a set of keys found by pkcrack, *encrypted_archive* is the name of the archive to be decrypted, and *decrypted_archive* is the name of the file to which the archive will be written by zipdecrypt.

One option to pkcrack has not been mentioned yet: with `-o offset` you can specify an offset of the plaintext data into the ciphertext. This is for the special case that the known plaintext starts somewhere in the middle of the encrypted data. The default value for offset is 0, i. e. the 12 encrypted random bytes are not to be included in the offset.

There is another possible application of the offset: the so-called "random" bytes aren't that random. Older versions of PkZip used the CRC-checksum of the file as the last 2 "random" bytes, newer versions use "only" one byte of CRC. In that case you have to prepend the known CRC-bytes before the known plaintext file, and specify a negative offset (e. g. -1 if you know the last of the "random" bytes). This feature has not been tested very thoroughly. Be warned.

What remains to be explained is the findkey program and its counterpart, the makekey program. You can use findkey to find a PkZip-password for a set of key[012]-values found by pkcrack. This is exactly what pkcrack does if you do not specify the -d option. Periodically, findkey prints information about its progress which can be used to restart the program at a later time. The information printed is of the form

```
  10: xx, or
  11: xxxx, or
  12: xxxxxx and so on.
```
To restart findkey, enter

``` sh
findkey key0 key1 key2 pwdlen initvalue
```
where *key0, key1, key2* is a set of keys found by pkcrack, pwdlen is 10 or 11 or 12 (depending on the point where you want to resume), and initvalue is the "xx" printed by findkey. *pwdlen* and *initvalue* are optional parameters.

Note that findkey will take **very long** to find long passwords. There are 255 times as many possible passwords with a pwdlen of 11 as with a pwdlen of 10. It is probably wiser to use the zipdecrypt program instead.

The makekey program can be used to generate *key0, key1 and key2* from a given password. You will not need it to break an encrypted file, but it's a nice tool for playing around with encrypted files.

## Demo

file structure: 
```
demo
├── demo.zip # encrypted-ZIP
├── pkcrack 
├── pkcrack.exe
├── README.txt # plaintext
└── README.zip # plaintext-ZIP 
``` 

the following shell command is used to crack:
```
../bin/pkcrack  -C demo.zip -c README.txt -P README.zip -p README.txt -d cracked.zip -a
```

the result will be:
```
Files read. Starting stage 1 on Thu Dec  7 12:45:53 2017
Generating 1st generation of possible key2_624 values...done.
Found 4194304 possible key2-values.
Now we're trying to reduce these...
Done. Left with 11054 possible Values. bestOffset is 24.
Stage 1 completed. Starting stage 2 on Thu Dec  7 12:46:07 2017
Ta-daaaaa! key0=be5382c6, key1= 750a330, key2=e7d4dbfe
Probabilistic test succeeded for 605 bytes.
Ta-daaaaa! key0=be5382c6, key1= 750a330, key2=e7d4dbfe
Probabilistic test succeeded for 605 bytes.
Stage 2 completed. Starting zipdecrypt on Thu Dec  7 12:47:34 2017
Decrypting CRACK.txt (aafa572da93cf74237fbca5d)... OK!
Decrypting README.txt (83c0bfb47b83166f9f43a365)... OK!
Finished on Thu Dec  7 12:47:34 2017
```

## Details

Here is a short description of the source-files:

- **crc.c** This file contains functions for calculating CRC-32 checksums. The CRC-polynomial used is defined in crc.h A lookup-table is used, which has to be initialized first.
- **crc.h** Header file for crc.c - this file contains macros for computing CRC-checksums using a lookup-table which has to be initialized using a function in crc.c
- **debug.c, debug.h** These two files define some functions that can help in debugging the stage1 and stage2 functions. They require the encryption keys to be known, and can check if all the intermediate values are computed correctly.
- **exfunc.c** This file contains a function for reading data from a ZIP-archive into memory.
- **extract.c** This file contains the main() function of the "extract" program, which may be used to extract data from a ZIP-archive and write it to a file.
- **findkey.c** This program tries to find a PkZip-password for a given initial state of key0, key1 and key2. In the current version it prints information about the progress of the search to stdout every couple of minutes. You can use that information for resuming the search at a later time.
- **headers.h** This headerfile contains declarations of several data types used in ZIP-archives.
- **keystuff.c** This file contains functions for initializing and updating the internal state of the PkZip cipher.
- **keystuff.h** This is a header file for keystuff.c
- **main.c** This file contains the main() function of the PkZip-cracker. It reads the ciphertext and plaintext files and makes calls to the actual cracking stages.
- **makekey.c** This file contains the main() function of the makekey program.
- **mktmptbl.c** This file contains a function for initializing a lookup-table that is used for finding "temp" values for a given "key3" (refer to the paper if you want to know what "temp" and "key3" are).
- **mktmptbl.h** This is a header file for mktmptbl.c
- **pkcrack.h** This header file contains some constants used in the program and some global variables from main.c
- **pkctypes.h** This header file contains the type definitions for byte, ushort and uword. This is probably something you should change when you port the software to another platform.
- **readhead.c** This file contains several functions for reading and parsing headers in a ZIP-archive. Refer to the file "appnote.iz.txt" for details.
- **stage1.c** This file implements stage 1 of the cracking process, namely finding initial values for key2_n and reducing the number of possible values. See sections 3.1 and 3.2 of the paper.
- **stage1.h** This is a header file for stage1.c
- **stage2.c** This file implements stage2 of the cracking process, namely creating lists of key2-values, calculating the corresponding key1 and key0 values, decrypting the 12 prepended bytes and finally calling stage3. See sections 3.3, 3.4 and 3.5 of the paper.
- **stage2.h** This is a header file for stage2.c
- **stage3.c** This file implements stage 3 of the cracking process, namely finding a PkZip-password for a given internal state of key0, key1 and key2. It re-uses some code from stage2.c See section 3.6 of the paper.
- **stage3.h** This is a header file for stage3.c
- **writehead.c** This file is the counterpart of readhead.c - it contains functions for writing headers in a ZIP-archive.
- **zdmain.c** This file contains the main function of the zipdecrypt program.
- **zipdecrypt.c** This file contains a function for decrypting a ZIP-archive with a given set of key[012] values. It produces a ZIP-archive which can be unzipped using pkunzip under DOS or unzip under UNIX. I wrote this be cause stage 3 (password generation) takes a couple of eons for finding long passwords.

That's it. Some of the less obvious sections of the code are commented. Most aren't.