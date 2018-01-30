# CTF_Practice

#### Challenges


#### 1. Hidden PDF
The fist flag was found in the header information for the presentation pdf. The steps for extracting it:

`strings presentation.pdf | grep CTF`

The first flag still has some characters in their ASCII octal form. The next step is to convert those using:

`printf 'ZeroCTF\173l00k\137c4r3fully\175'

The result is:

`ZeroCTF{l00k_c4r3fully}`
  
