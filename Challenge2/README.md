# CTF_Practice - Chall3nge 0ne


#### FLAGS

| Challenge      | Flags         | Points|
| -------------  |:-------------:| ---: |
| *Presentation*   | |
|                | ZeroCTF{t0p s3kr1t} | 50 |
| *hax.jpg*       |    |
|  | ZeroCTF{st3g0s4urus_r3x} | 100 |
|  | ZeroCTF{c0ntr4st} | 100 |
|  **Total**  |   | 150 |


#### Challenges


#### 1. Hidden PDF
```
The easiest flag to find in the presentation pdf is in plain text with but with a white font color on a white background. To find the flag, I did a search in page for `CTF{`
revealing the flag:

`ZeroCTF{t0p s3kr1t}`

```

#### 2. hax.jpg
```

The clue in the attached README gives this one away pretty quickly. Stuffanography? Since I know one of the most common steganography techniques
is to `cat` together an image and a zip file, I took a quick glance that the bottom of the image using strings.

`strings hax.jpg` 

This reveals what looks like a directory structure:

decryptme/UT

decryptme/flag.txt

Since I know this file can be handled by `unzip` this is first thing I try.

`unzip hax.jpg`

Unfortunately, the file in the zip is password protected. Great! This means I will have to either crack it using something like `fcrackzip`, or hope that a password is stashed somewhere else.
Since I do not know the length of the password protecting the file, I opted for searching around first. This step was both the most frustrating and best telling
of my lack of confidence in running through these exercises. Since I have know idea where the password may actually be, I felt crushed by the hopelessness.

I took a drink of my sparkling water and headed onward into the dark.

The first step was to see if `password` just so happened to be somewhere in the file:

`strings hax.jpg | grep pass`

`strings hax.jgp | grep p@ss`

`strings hax.jpg | grep p@$$`

F&#$! It wasn't going to be that easy. With no clear way of finding it, I opted for a quick scan of the head of the file to see if anything sneaky looked like a password:

`strings hax.jpg | head -5`

The second line of the output seemed passwordy enough to pass most of the dumb, usually unguided, password rules from my company's IT password change tool.

`Ahgho6lipa4iChu0`

I gave this password to the unzip->password prompt, and Voila!

`cat decryptme/flag.txt`

The flag is : ZeroCTF{st3g0s4urus_r3x}
 

```

``` 

The second flag is actually easier to find, and is in the image. Manipulating image color values, I was able to find the flag:

`ZeroCTF{c0ntr4st}`

```
