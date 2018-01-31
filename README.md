# CTF_Practice


#### FLAGS

| Challenge      | Flags         | Points|
| -------------  |:-------------:| ---: |
| *Presentation*   | |
|                | ZeroCTF{l00k_c4r3fully}| 50 |
|                | ZeroCTF{THi5 i5 A fLaG}      | 50 |
|                | ab0bfd73daaec7912dcdca1ba0ba3d05 => flag1  | 50 |
|                | ZeroCTF{sw1gg1ty sw00ty}     | 50 |
| *secretum*       |    |
|  | ZeroCTF{c4rpe_v3xi11um} | 100 |
|  **Total**  |   | 300 |



#### Challenges


#### 1. Hidden PDF
```
The fist flag was found in the header information for the presentation pdf. The steps for extracting it:

`strings presentation.pdf | grep CTF`

The first flag still has some characters in their ASCII octal form. The next step is to convert those using:

`printf 'ZeroCTF\173l00k\137c4r3fully\175'

The result is:

`ZeroCTF{l00k_c4r3fully}`
```

```
The second flag is in plain text in the presentation:

`ZeroCTF{THi5 i5 A fLaG}`
```

```
The third flag in the presentation comes from the reverse of the md5 hash for 'ab0bfd73daaec7912dcdca1ba0ba3d05'
`ab0bfd73daaec7912dcdca1ba0ba3d05 MD5 : flag1`
```

```
What is this, a flag for ant's? The fourth flag was in unlegibly small text in the presentation
`ZeroCTF{sw1gg1ty sw00ty}`
```

#### 2. Secretum
```
The first flag from this file was found using a search for the known flag pattern:

`strings secretum | grep {*}`

This fortunately only produces the output:

`FkxuIZL{i4xvk_b3do11as}`

Given the "clue" of Caesar in the original challenge file, I applied a Caesar Cipher on the output.
Since I know the form of the flag, Z=F and I assume A=G. I then filter the results using `tr` to do my alphabetic shift:

`strings secretum | grep {*} | tr g-za-fG-ZA-F a-zA-Z`

This results in the clear text flag:

`ZeroCTF{c4rpe_v3xi11um}`
