# CTF_Practice - Chall3nge Thr33


#### FLAGS

| Challenge      | Flags         | Points|
| -------------  |:-------------:| ---: |
| *flag.txt*       |    |
|  | ZeroCTF{r0und_4nd_r0und} | 100 |
|  | ZeroCTR{Ju5t_j4mm1n} | 100 |
|  **Total**  |   | 200 |


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

With this difference in size, I took a chance that this really was a matryoshka dolls of compression following the same pattern, and wrote up a quick script:

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

#### 2.out

The first challenge lead directly to a tar file containing the next step. The contents of this file were curious:

```
$tar -xvf 3-1.tar.gz
$ls
3-1.tar.gz  enc.pl  out
```

I took a quick peak in the `out` file first.


```
$cat out
^bxnKUH▒Vz;{g{>xyF|▒8>:▒▒▒▒▒ZHI▒▒▒L▒▒▒▒▒▒▒T▒▒▒Y▒▒▒▒▒o▒o▒▒~▒}▒▒▒v▒▒P
```

After a quick investigation of the `out` file to ensure our challenger was done with his dolls, it fortunately turned out to be a binary-fied text file.

I next looked into the perl script `enc.pl`:

```

#!/usr/bin/env perl

use warnings;
use strict;

if(@ARGV != 1){
    die();
}

open(my $in, "<", $ARGV[0]);
open(my $out, ">", "out");

if(!defined($in) || !defined($out)){
    die();
}

my $rando = int(rand(11));

my $ctr = 0;
while(read($in, my $buf, 1) != 0){
    my $o = chr((ord($buf) ^ $rando) + $ctr);
    $ctr++;
    print $out $o;
}

close($in);
close($out);


```

It looks like a simple enough PERL script that: takes a single file as input, does some bitwise math on the read input character's binary, and then pumps the new boggled binary data back out to a file called `out`. This gives me the source of where the `out` file originated, and I can only assume it is my job to reverse the bit jumbling to reveal the original text.

Now my one class in boolean algebra should save me here, but a quick Google gave me some quick tips on XOR permutations. Lets take a look at a basic example below:

```
a=b^c

b=c^a
c=b^a
```

Ok. So if my math is right, the bitwise function in the perl script should translate as follows:

```
my $o = chr((ord($buf) ^ $rando) + $ctr);

a = (b ^ c) + d
```

In this case, we are looking for the original character value which would be represented by `b` above.  Some of my own quick algebra which I did not fully trust told me that I could inverse this with:

```

b = (c ^ (a-d))

my $o = chr($rand ^ (ord($buf) - $ctr))

```

Sweet. At this point, I need to replace the original bitwise manipulation with my reversal, and I will get the decyphered message. The only real problem I face now is the original message was cyphered with a random number:

```
my $rando = int(rand(11));
```

Since the number is within a decently manageable range, I manually ran through the iterations making slight modifications to the original script incrementing the `$rando` number by 1 each time. Finally after the 4 iteration, I was able to pull the flag out of the file:

```
ZeroCTF{Ju5t_j4mm1n} - https://www.youtube.com/watch?v=xTA_FexW3qU
```

#### Bonus
Here is the very rough reversal script used to crack the encryption:

```
#!/usr/bin/env perl

use warnings;
use strict;

my ($in_a, $out_a) = @ARGV;

open(my $in, "<", $in_a);
open(my $out, ">", $out_a);

if(!defined($in) || !defined($out)){
    die();
}

#my $rando = int(rand(11));

my $rando = 4;

my $ctr = 0;
while(read($in, my $buf, 1) != 0){
    #my $o = chr((ord($buf) ^ $rando) + $ctr);
    my $o = chr($rando ^ (ord($buf) - $ctr));
    $ctr++;
    print $out $o;
}

close($in);
close($out);
```


