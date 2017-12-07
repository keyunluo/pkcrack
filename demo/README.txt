Hello, 
This is the Plain Text File
Used by Pkcrack

With version 1.2 pkcrack has become one of those "fire & forget" programs. If you are a hacker of the experimental kind you may want to look at the "more complete instructions" below. Otherwise, stick to the "simple instructions".
The first thing you have to know is that this program applies a known plaintext attack to an encrypted file. A known-plaintext-attack recovers a password using the encrypted file and (part of) the unencrypted file. Please note that cryptographers use the word 'plaintext' for any kind of unencrypted data - not necessarily readable ASCII text.

Before you ask why somebody may want to know the password when he already knows the plaintext think of the following situations:

Usually there's a large number of files in a ZIP-archive. Usually all these files are encrypted using the same password. So if you know one of the files, you can recover the password and decrypt the other files.
You need to know only a part of the plaintext (at least 13 bytes). Many files have commonly known headers, like DOS .EXE-files. Knowing a reasonably long header you can recover the password and decrypt the entire file.
Back to the program.