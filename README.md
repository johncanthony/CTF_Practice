# CTF_Practice

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
