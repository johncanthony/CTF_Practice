# CTF_Practice - Chall3nge Tw0


#### FLAGS

| Challenge      | Flags         | Points|
| -------------  |:-------------:| ---: |
| *flag.txt*       |    |
|  | ZeroCTF{r0und_4nd_r0und} | 100 |
|  **Total**  |   | 100 |


#### Challenges


#### 1. flag.txt

The initial investigation of this file reveals some interesting things. A quick `strings` of the file tips me off that this is some form of a compressed file. This is confirmed by a quick `binwalk`.

```
binwalk flag.txt

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             POSIX tar archive (GNU)
```

Great, this is a tar; easy peasy! 


But wait, there's more.


I used tar to extract this file just to find another file in its place called ..... bump bump a dum .... flags.txt. This was curious, and so I did another quick binwalk
to find that this new file flag.txt was a zip. TFW. I unzip the file using `unzip`, and to find another file called flags.txt. The redundancy is not lost on me.


Another binwalk of this file showed a .gz zipped file still labeled flag.txt. Our challenge creator seems to have a thing for matryoshka dolls. Since `gunzip` is picky about
the extension, I `mv` the file over to flag.gz then gunzip the file. The next logical step is another binwalk, and as suspected we have another compression. Ladies and gentlemen,
let me introduce .bz2.


Again I change the extension to keep bzip2 from griping and decompress:

```
mv flag flag.bz2

bzip2 -d flag.bz2
```

At last, I am staring at flag.txt. The day is here and all is good with the world. I cat the file, and reach a new level of disappointment. With another binwalk of the file,
I find myself right back at step 1. The file is now showing as a POSIX tar archive again.

Needless to say, I thought this was some extreme black magic of compressions somehow hacked to become other compressions. Some more investigation of this new starting point needed to happen.

To dispell my black magic theory, I did a quick md5sum on the file to ensure it was indeed different. The hashes did not line up, and I sighed a little. I then took a hunch and compared
the file size of the new flag.txt against a copy of the original, and found that the new file was reduce in size by 240B.

```
$ ls -la flag_orig.txt
-rw-r--r-- 1 root root 1320960 Feb 27 20:44 flag_orig.txt

ls -la flag.txt
-rw-r--r-- 1 root root 1310720 Feb 25 22:55 flag.txt
```

With this difference in size, I took a chance that this really was a matryoshka dolls of encryption following the same pattern, and wrote up a quick script:

```
#!/bin/bash

tar -xvf flag.txt
unzip -o flag.txt
mv flag.txt flag.gz
gunzip flag.gz
mv flag flag.bz2
bzip2 -d flag.bz2
mv flag flag.txt
```

Running this through a loop a few hundred times, finally produced a flag.txt that was a genuine bonafied text file. Inside lived the first flag:

```
ZeroCTF{r0und_4nd_r0und} - https://www.youtube.com/watch?v=N8krUlRjRTI

There's a part 1 (we're counting up from 0) to this one. Hope your perl isn't
too rusty.

https://zer0-one.net/3-1.tar.gz
```
