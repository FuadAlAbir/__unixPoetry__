<pre>
<b>Unix fox Poets</b>
Source: https://www.cs.upc.edu/~padro/Unixforpoets.pdf

<b>Exercises:</b>
1. Count words.
2. Sort words. [ascii, dictionary, rhyme]
3. Extract info from dictionary.
4. Ngram Statistics.
5. Concordance.

<span style="color:red;">
<b>Tools:</b>
* grep (search for pattern - re)
* sort
* uniq -c (count duplicates)
* tr (translate characters)
* wc (word count)
* sed (edit string)
* awk (simple programming language)
* cut
* paste
* comm
* join</span>


<b>Glue:</b> | &lt; &gt;


<b>Exercise 0. File READ-WRITE</b>
<b>Read the file genesis.txt & print first 10 lines.</b>
    <b>$ sed 10q < genesis.txt</b>

<b>Do some operations and write the result in a file.</b>
    <b>$ tr 'A-Z' 'a-z' &lt; genesis.txt | tr -sc 'a-z' '\012' | sort | uniq -c &gt; word_count.txt</b>

<b>Note that, &lt; is used for READ and &gt; is used for WRITE.</b>


<b>Exercise 1. Count words in text</b>
    <b>$ sed 10q &lt; genesis.txt</b>
    Book 01        Genesis

    01:001:001 In the beginning God created the heaven and the earth.

    01:001:002 And the earth was without form, and void; and darkness was
            upon the face of the deep. And the Spirit of God moved upon
            the face of the waters.

    01:001:003 And God said, Let there be light: and there was light.

    <b>$ tr -sc 'A-Za-z' '\012' &lt; genesis.txt | sed 10q</b>

    Book
    Genesis
    In
    the
    beginning
    God
    created
    the
    heaven

    <b>$ tr -sc 'A-Za-z' '\012' &lt; genesis.txt | sort | sed 5q</b>

    a
    a
    a
    a

    <b>$ tr -sc 'A-Za-z' '\012' &lt; genesis.txt | sort | uniq -c | sed 10q</b>
      1 
    339 a
      2 A
      3 abated
      8 Abel
      1 Abelmizraim
      1 Abidah
      4 abide
      1 Abide
      1 Abimael

<b>SOLUTION:</b>
    <b>$ tr -sc 'A-Za-z' '\012' &lt; genesis.txt | sort | uniq -c</b>


<b>Exercise 2.1 More Counting: Merge the counts for upper and lower case</b>

    <b>$ tr 'A-Z' 'a-z' &lt; genesis.txt | sed 10q</b>
    book 01        genesis

    01:001:001 in the beginning god created the heaven and the earth.

    01:001:002 and the earth was without form, and void; and darkness was
            upon the face of the deep. and the spirit of god moved upon
            the face of the waters.

    01:001:003 and god said, let there be light: and there was light.

    <b>$ tr 'A-Z' 'a-z' &lt; genesis.txt | tr -sc 'a-z' '\012' | sort | uniq -c | sed 10q</b>
      1 
    341 a
      3 abated
      8 abel
      1 abelmizraim
      1 abidah
      5 abide
      1 abimael
     24 abimelech
      3 able

<b>SOLUTION:</b>
    <b>$ tr 'A-Z' 'a-z' &lt; genesis.txt | tr -sc 'a-z' '\012' | sort | uniq -c</b>


<b>Exercise 2.2 More Counting: Count sequences of vowels</b>
    <b>$ tr 'A-Z' 'a-z' &lt; genesis.txt | tr -sc 'aeiou' '\012' | sort | uniq -c | sed 100q</b>
      1 
  12795 a
    150 aa
     65 ae
    793 ai
     99 ao
    277 au
  16119 e
    993 ea
      1 eau
    679 ee
      8 eei
    277 ei
     78 eo
     15 eou
     24 eu
      5 eue
   6707 i
     44 ia
    266 ie
    115 io
      4 iou
      1 iu
   7953 o
     90 oa
     24 oe
     57 oi
      2 oii
    276 oo
   1254 ou
   1868 u
     20 ua
     33 ue
     62 ui
     
<b>SOLUTION:</b>
    <b>$ tr 'A-Z' 'a-z' &lt; genesis.txt | tr -sc 'aeiou' '\012' | sort | uniq -c</b>

<b>Exercise 3. Sorting Exercises</b>
<b>Sort the words in Genesis by freq</b>
    <b>$ tr 'A-Z' 'a-z' &lt; genesis.txt | tr -sc 'a-z' '\012' | sort | uniq -c | sort -nr &gt; genesis_freq.hist</b>

<b>Sort them by rhyming order</b>
    <b>$ tr 'A-Z' 'a-z' &lt; genesis.txt | tr -sc 'a-z' '\012' | sort | uniq -c | rev | sort | rev | sed 10q</b>
      1 
    341 a
      3 sheba
     11 beersheba
      1 seba
      1 kirjatharba
      8 sea
      4 yea
      1 lasha
      1 mesha
      
<b>Hint:</b>
    <b>$ echo hello world | rev</b>
    dlrow olleh
    <b>$ echo hello world | rev | rev</b>
    hello world


<b>Exercise 4. Bigrams (word pair)</b>
Algorithm:
    ~ tokenize by word
    ~ print word i and word i + 1 on the same line
    ~ count
    
    <b>$ tr 'A-Z' 'a-z' &lt; genesis.txt | tr -sc 'A-Za-z' '\012' &gt; genesis.words</b>
    <b>$ tail +2 genesis.words &gt; genesis.nextwords</b>
    <b>$ paste genesis.words genesis.nextwords</b>
    ...
    the     children
    children        of
    of      israel
    israel  saying
    saying  god
    god     will
    will    surely
    surely  visit
    visit   you
    ...
    <b>$ paste genesis.words genesis.nextwords | sort | uniq -c | sort -nr &gt; genesis.bigrams</b>

<b>Exercise 4.1 Trigrams</b>
    <b>$ tail +2 genesis.nextwords &gt; genesis.afternextwords
    $ paste genesis.words genesis.nextwords genesis.afternextwords | sort | uniq -c |
        sort -nr &gt; genesis.trigrams</b>


<b>Exercise 5. Shell Script</b>
    [trigram.sh]
        tr 'A-Z' 'a-z' | tr -sc 'a-z' '\012' &gt; $$words 
        tail +2 $$words &gt; $$nextwords
        tail +3 $$words &gt; $$next2words
        paste $$words $$nextwords $$next2words | sort | uniq -c | sort -nr
        rm $$words $$nextwords $$next2words

    <b>$ sh trigram.sh &lt; genesis.txt &gt; new.trigrams</b>


<b>Exercise 6. grep & egrep</b>
    $ grep 'the land of' genesis.txt 
    [figure]

    $ grep 'the land of' genesis.txt | sh trigram.sh | sort -nr | sed 5q
        93 the     land    of
        47 in      the     land
        30 land    of      egypt
        24 land    of      canaan
        11 of      the     land
     
    $ grep 'and he said' genesis.txt | sh trigram.sh | sort -nr | sed 5q
       15 and     he      said
        4 said    unto    him
        2 son     and     he
        2 said    here    am
        2 my      son     and
      
$ grep 'in the name'    : find lines containing 'in the name'
$ grep '^the'           : find lines starting with 'the'
$ grep 'ing$'           : find lines ending with 'ing'
$ grep ’ˆ[A–Z]*$’       : find lines with all uppercase
$ grep '[^A-Z]'         : find lines with non uppercase

$ grep –i ’[aeiou].*[aeiou]’              : lines with two or more vowels
$ grep –i ’ˆ[ˆaeiou]*[aeiou][ˆaeiou]*$’   : lines  with exactly one vowel 

a               : match the letter ‘‘a’’
[a–z]           : match any lowercase letter
[A–Z]           : match any uppercase letter
[0–9]           : match any digit
[0123456789]    : match any digit
[aeiouAEIUO]    : match any vowel

[ˆaeiouAEIOU]   : match any letter but a vowel
.               : match any character
ˆ               : beginning of line
$               : end of line
x*              : any number of x
x+              : one or more of x (egrep only)
x|y             : x or y (egrep only)
(x)             : override precedence rules (egrep only)

-v : not containing
-c : count
-i : ignore case

<b>Exercise</b>
<b>1. How many uppercase words are there in Genesis? Lowercase?</b>
<b># by token</b>
	<b>$ tr -sc '[A-Z][a-z]' '\012' &lt; genesis.txt | grep -c '^[A-Z]'</b>
	5531
<b># by type | -u to eliminate duplicates</b>
	<b>$ tr -sc '[A-Z][a-z]' '\012' &lt; genesis.txt | sort -u | grep -c '^[A-Z]'</b>
	634
<b># lowercase by token, we will consider tokens now on for this exercise.</b>
	<b>$ tr -sc '[A-Z][a-z]' '\012' &lt; genesis.txt | grep -c '^[^A-Z]'</b>
	32991

<b>2. How many 4-letter words?</b>
    <b>$ tr -sc '[A-Z][a-z]' '\012' &lt; genesis.txt | grep '^....'</b>
    [figure]
    <b>$ tr -sc '[A-Z][a-z]' '\012' &lt; genesis.txt | grep '....$'</b>
    [figure]
    <b>$ tr -sc '[A-Z][a-z]' '\012' &lt; genesis.txt | grep '^....$'</b>
    [figure]

<b>SOLUTION:</b>
    <b>$ tr -sc '[A-Z][a-z]' '\012' &lt; genesis.txt | grep -c '^....$'</b>
        9040

<b>3. Are there any words with no vowels?
    $ tr -sc '[A-Z][a-z]' '\012' &lt; genesis.txt | grep -i '^[^aeiou]*$' | sort | uniq -c</b>
      1 
     80 by
      4 By
      4 cry
      4 dry
      1 fly
    325 my
     18 My
      2 myrrh
    256 s
    267 thy
     11 Thy
      5 why
      3 Why
      
    <b>$ tr -sc '[A-Z][a-z]' '\012' &lt; genesis.txt | grep -vi '[aeiou]' | sort | uniq -c</b>
      1 
     80 by
      4 By
      4 cry
      4 dry
      1 fly
    325 my
     18 My
      2 myrrh
    256 s
    267 thy
     11 Thy
      5 why
      3 Why
      
<b>4. Find ‘‘1-syllable’’ words (words with exactly one vowel)
    $ tr -sc '[A-Z][a-z]' '\012' &lt; genesis.txt | grep -i '^[^aeiou]*[aeiou][^aeiou]*$' |
        sort | uniq -c | sed 5q</b>
    339 a
      2 A
      1 add
    245 all
      5 All
      
<b>5. Find ‘‘2-syllable’’ words (words with exactly two vowels)
    $ tr -sc '[A-Z][a-z]' '\012' &lt; genesis.txt | grep -i '^[^aeiou]*[aeiou][^aeiou]*[aeoiu][^aeiou]*$' |
        sort | uniq -c | sed 5q</b>
      8 Abel
      3 able
     59 Abram
      1 absent
      1 Accad
      
<b>
6.0 Some words with two orthographic vowels have only one phonological vowel.
6.1 Delete words ending with a silent ‘‘e’’ from the 2-syllable list.
    $ tr -sc '[A-Z][a-z]' '\012' &lt; genesis.txt | grep -i '^[^aeiou]*[aeiou][^aeiou]*[aeoiu][^aeiou]*$' |
        grep -v 'ee$' | sort | uniq -c
    
6.2 Delete diphthongs (sequences of two vowels in a row).
    $ tr -sc '[A-Z][a-z]' '\012' &lt; genesis.txt | grep -i '^[^aeiou]*[aeiou][^aeiou]*[aeoiu][^aeiou]*$' |
        grep -v '[aeiou][aeiou]' | sort | uniq -c</b>
    
<b>
7.0 Find verses in Genesis with the word ‘‘light.’’
    $ grep 'light' genesis.txt
7.1 How many have two or more instances of ‘‘light’’?
    $ grep -c 'light.*light' genesis.txt
7.2 Three or more?
    $ grep -c 'light.*light.*light' genesis.txt
7.3 Exactly two?
    $ grep 'light.*light' genesis.txt | grep -vc 'light.*light.*light'</b>
    
    
<b>Exercise 7. sed (string editor)</b>
    
    to be continued...
</pre>
