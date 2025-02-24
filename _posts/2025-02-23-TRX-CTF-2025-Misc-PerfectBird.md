---
title: "TRX CTF 2025 Misc - Perfect Bird"
read_time: false
comments: false
share: false
related: false
categories:
  - CTF
  - International
  - Team
tags:
- Misc
toc: true
toc_label: "Challenges"
toc_icon: "list"
---

<img src="/assets/images/trx25/flag2.png" alt="">
<img src="/assets/images/trx25/flag3.png" alt="">


Played this CTF together with RE:UN10N Malaysia members. This CTF is really hard + many RE:UN10N members are busy so mostly the not so strong RE:UN10N members like myself doing it. But its okay because my only goal is to get better. Fighting!!

---

## Misc

---

### Perfect Bird
<br>
<img src="/assets/images/trx25/flag.png" alt="">
**Description:**<br>
As a <var>bird</var> soaring through the sky, you seek the <var>perfect language</var>, and then... you find this

**Flag:**
TRX{tHi5_I5_th3_P3rf3ct_l4nGU4g3!!!!!!}

They gave `chall.db3` file

Reading the file and first time seeing this language.

```markdown
var var 42<Infinity> = 0!

functi main () => {
      const var v = m[a-1]!!!
const var w = ((42 ^ v) % 256) ^ 0x48!!!!
      m[a-1] = w!!!!!!!!!!
      const var a<-3> = 42 % 39!!
      42 += 1!!!!
   if (;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;42) {
      const var v = m[a-1]!!
      const var w = ((42 ^ v) % 256) ^ 0x89!!!
m[a-1] = w!!!!
const var a<-3> = 42 % 39!!
42 += 1!
   }
   if (;;;;;;42) {
    const var v = m[a-1]!!!!!!!!
      const var w = ((42 ^ v) % 256) ^ 0x54!!!
m[a-1] = w!!!!!!!!!!
      const var a<-3> = 42 % 39!
42 += 1!!!!!
   }
   if (;;;;;;;;;;;;;;;;;;;;;;;;42) {
const var v = m[a-1]!!!!!!!!!
      const var w = ((42 ^ v) % 256) ^ 0x24!!!
      m[a-1] = w!!!!
      const var a<-3> = 42 % 39!!!!
42 += 1!!!!
...
const var a<-3> = 42 % 39!!!!!!!
42 += 1!!!
   }
      const const const m<-34434> = [205, 242, 231, 208, 235, 150, 5, 14, 162, 115, 134, 81, 118, 138, 49, 16, 54,142,80, 102, 139, 35, 127, 83, 52, 106, 200, 185, 153, 203, 34, 66, 62, 12, 7, 166, 34, 81, 250]!
   print(m)!
}
```

The file is really long and mostly the same thing, or its looping? But this language is easy to read. So I start with some OSINT to search for this language. Then I found a github link:
https://github.com/TodePond/GulfOfMexico and in the github theres also a link to another github where it shows how to run this language: https://github.com/vivaansinghvi07/dreamberd-interpreter/ .
If you notice there are two name, Gulf of Mexico and DreamBerd. After some investigating DreamBerd is the old name.

But how do I know this is the language? There are two hints in the challenge description, "bird" and "perfect language". So I think DreamBerd is the "bird" whereas for "perfect language", well, the README.md of the github it says "Gulf of Mexico is a perfect programming language." And if you scroll a bit the language can use multiple !!!!!! and there are const const so its the same as out challenge file .db3.

So start by reading the whole github to make sense of the language. I think this challenge code can be divided into 4 main snippet.
```markdown
var var 42<Infinity> = 0!
```
First one is declaring 42 as a variable. It uses <var>var var</var> so that variable 42 can be re-assigned and edited. And also, this language can have number as variable. <> tag after a variable is their lifespan. The infinity tag indicates that variable 42 will last until the end of the program. Then it assign value 0 to variable 42. the "!" act as a separator that is used to terminate a statement. Basically its the same as ";" in java but in this language, we can be bold!!!!!!!!!!!!!!!!!!!!!. If youre confident that line has no problem then use !!!!!!!!!!!!!!!!!!!!, if not use ?.

```markdown
functi main () => {
```
this is declaring main() function. but why not "function". Well, we can use any letters from the word function but in order such as func, fun, fn, functi, f.

```markdown
const var v = m[a-1]!!!
const var w = ((42 ^ v) % 256) ^ 0x48!!!!
      m[a-1] = w!!!!!!!!!!
      const var a<-3> = 42 % 39!!
      42 += 1!!!!
   if (;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;42) {
      const var v = m[a-1]!!
      const var w = ((42 ^ v) % 256) ^ 0x89!!!
m[a-1] = w!!!!
const var a<-3> = 42 % 39!!
42 += 1!
   }
...
```
There are no loop in this language. <var>const var v = m[a-1]!!!</var> Here, const var is used to declare a variable that is mutable (can be edited, but not re-assigned).
The expression m[a-1] accesses an element of the array m. In Gulf of Mexico arrays start at index -1 instead of 0.

<var>const var w = ((42 ^ v) % 256) ^ 0x48!!!!</var> computes a new value w by taking the bitwise XOR (the ^ operator) of 42 and v, applying a modulo 256, and then XORing again with 0x48 (48h) (hex 48).

<var>m[a-1] = w!!!!!!!!!!</var> then it transfer the value to m[a-1]

<var>const var a<-3> = 42 % 39!!</var> Specifying a negative lifetime to make a variable exist before its creation, and disappear after its creation. here its just assigning 42 modulus 39 to variable a.

<var>42 += 1!!!!</var> The variable 42 initially is 0 then plus one. I think its like i+1 in loop.

<var>if (;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;42)</var> Semicolons serve as the “not” operator. The repeated semicolons don’t change the logic like exclamation mark. So inside all <var>if</var> block, a variable v is re-declared to grab the current value from the array, a new w is computed using a similar formula, but with a different hexadecimal constant (0x89, 0x54, 0x24 and many more!!!!!!!!!!), the array element is updated with the new w, then lastly A new variable with a lifetime specification is declared, and then the literal 42 is incremented again.

```markdown
    const const const m<-34434> = [205, 242, 231, 208, 235, 150, 5, 14, 162, 115, 134, 81, 118, 138, 49, 16, 54,142,80, 102, 139, 35, 127, 83, 52, 106, 200, 185, 153, 203, 34, 66, 62, 12, 7, 166, 34, 81, 250]!
print(m)!
```
by the look of it you can already see that these numbers is the flag. it store these numbers to m then it just print it. So I think we just need to just run the code? But its not as easy as that. First when trying to run it, you will get error
```bash
─$ dreamberd chall.db3
dreamberd.base.InterpretationError: __unnamed_file__, line 34434

       const const const m<-34434> = [205, 242, 231, 208, 235, 150, 5, 14, 162, 115, 134, 81, 118, 138, 49, 16, 54, 142, 80, 102, 139, 35, 127, 83, 52, 106, 200, 185, 153, 203, 34, 66, 62, 12, 7, 166, 34, 81, 250]!
  ^^^^^
Invalid indenting detected (must be a multiple of 3). Tabs count as 2 spaces.
```
This is because all indents must be 3 spaces long. So just add one extra space at <var>const const const m</var> line. Then try to run again.
```bash
$ dreamberd chall.db3
Warning: Public global variable `pivo` access failed.
Code has finished executing. Press ^C once or twice to stop waiting for when-statements and after-statements.
```
It doesnt print anything. I tried to print the m but I failed. Finally I just mimics the code to a python code.

```python
m = [205, 242, 231, 208, 235, 150, 5, 14, 162, 115,
     134, 81, 118, 138, 49, 16, 54, 142, 80, 102,
     139, 35, 127, 83, 52, 106, 200, 185, 153, 203,
     34, 66, 62, 12, 7, 166, 34, 81, 250]

# This is variable 42
counter = 0

# Put ALL the hexadecimal constants extracted from each transformation block. Its veryy long.
keys = [0x48,0x89,0x54,0x24,...,0xff,0x84,0xc0,0x75]

# Just simulate the basic XOR. the <> tag after variable can just ignore. Basically there are amny things can ignore like !!!
for key in keys:
    a = counter % 39
    i = a
    v = m[i]
    w = ((counter ^ v) % 256) ^ key
    m[i] = w
    counter += 1

print("m =", m)
```

Running the script got ascii array.
```bash
$ python chall.py
Final m = [84, 82, 88, 123, 116, 72, 105, 53, 95, 73, 53, 95, 116, 104, 51, 95, 80, 51, 114, 102, 51, 99, 116, 95, 108, 52, 110, 71, 85, 52, 103, 51, 33, 33, 33, 33, 33, 33, 125]
```

Then use cyberchef or add another function in the python to decrypt it

<img src="/assets/images/trx25/image1.png" alt="">

--The End--
{: style="text-align: center;"}