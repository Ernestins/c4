# c4

A Macro Language (usable like GNU m4) with template engine function and influence from m4, awk, ruby, razor, cpp (c preprocessor) and perl

> while **m4** and **cpp** are nice because they are simple. They are not enough to handle markdown.
>
> **awk** is nice to proceed some database like information, which can split in fields and rowsets.
>
> **razor** is a very nice and powerful **template engine**.
>
> **perl** can handle **regex** / **pcre** in the best way.
>
> **ruby** has some nice extention methods like .each which will be a benefit.
>
> so mixin all these pros to a new one which will be able to handle nested (recursive) markup/markdown in hopefully a simple to read simple to extend syntax called **c4**.

## A simple m4 Headings Numbering

### The m4 Macro Definition:
```m4
divert(-1)

# The H2_COUNT macro is redefined every time the H2 macro is used:
define(`H2_COUNT', 0)

define(`H2',
	`define(`H2_COUNT', incr(H2_COUNT))<h2>H2_COUNT. $1</h2>')

divert(1)dnl
```

### The m4 Macro calls:
```m4
H2(Erstes Kapitel)
H2(Zweites Kapitel)
H2(Schluss)
```

### The m4 HTML output:
```html
<h2>1. Erstes Kapitel</h2>
<h2>2. Zweites Kapitel</h2>
<h2>3. Schluss</h2>
```

---


## A simple c4 Headings Numbering

### The c4 Macro Definition:
```c4
h2($*):=> head2($*);

begin() {
  $h2_counter = 0;
}

head2(mark content) {
  <h2>@++$h2_counter. content</h2>
}
```
`h2($*)` matched the `h2` macro and provides all inner data by $*.
with the Brakets `(` and `)` a possible macro nesting is clear.

> In this scenario it would be possible to rename head2 to h2 and no 
> macro mapping in line 1 is necessary !


### The c4 Macro calls:
```m4
h2(Erstes Kapitel)
h2(Zweites Kapitel)
h2(Schluss)
```

### The c4 HTML output from Markdown:
```html
<h2>1. Erstes Kapitel</h2>
<h2>2. Zweites Kapitel</h2>
<h2>3. Schluss</h2>
```


---


## A simple c4 Headings Numbering from Markdown Source

### The c4 Macro Definition:
```c4
match(/^## $*$/):=> head2($*);

begin() {
  $h2_counter = 0;
}

head2(mark content) {
  <h2>@++$h2_counter. content</h2>
}
```
with `match(/.../)` in a macro mapping it would be possible to catch text by regular expressions!

### The Markdown:
```md
## Erstes Kapitel
## Zweites Kapitel
## Schluss
```

### The c4 HTML output from Markdown:
```html
<h2>1. Erstes Kapitel</h2>
<h2>2. Zweites Kapitel</h2>
<h2>3. Schluss</h2>
```




---

### A little more complex c4 Macro Definition:
which will reset the counter by each parent heading (h1):

```c4
#!/usr/bin/c4 -w
#h1h2.c4

 match(/^# $*$/):=> head1($*);
match(/^## $*$/):=> head2($*);

begin() {
  $h1_counter = 0;
  $h2_counter = 0;
}

head1(mark content) {
  $h2_counter = 0;  
  <h1>@++$h1_counter. content</h1>
}

head2(mark content) {
  <h2>@++$h1_counter.@++$h2_counter. content</h2>
}
```

### The Markdown:
```md
# Erstes Kapitel
## Erste Sektion
## Zweite Sektion
## Zusammenfassung

# Zweites Kapitel
## Erste Sektion
## Zweite Sektion
```

### The c4 HTML output from Markdown:
```html
<h1>1. Erstes Kapitel</h1>
<h2>1.1. Erste Sektion</h2>
<h2>1.2. Zweite Sektion</h2>
<h2>1.3. Zusammenfassung</h2>
<h1>2. Zweites Kapitel</h1>
<h2>2.1. Erste Sektion</h2>
<h2>2.2. Zweite Sektion</h2>
```

### The cli for m4 -> html transformation

```sh
$ cat capter.md | c4 -m h1h2.c4 
<h1>1. Erstes Kapitel</h1>
<h2>1.1. Erste Sektion</h2>
<h2>1.2. Zweite Sektion</h2>
<h2>1.3. Zusammenfassung</h2>
<h1>2. Zweites Kapitel</h1>
<h2>2.1. Erste Sektion</h2>
<h2>2.2. Zweite Sektion</h2>
```



---

Take a look how a for-loop will be written in 

C++ for:
```cpp

for(int i=0; i<10; ++i)
{
  cout << i << "\n";
}

```



For-Loop in M4:
```m4
define(`for',
   `ifelse($#,0,``$0'',
   `ifelse(eval($2<=$3),1,
   `pushdef(`$1',$2)$5`'popdef(`$1')$0(`$1',eval($2+$4),$3,$4,`$5')')')')dnl

for(`x',`1',`5',`1',
   `for(`y',`1',x,`1',
      `*')
')
```

Native Loop in c4 via gotos and then a template which creates an ordinary for-loop template for c4:
```c4

{
  $i=0;
  label continue: if(expr !($i<10)) goto break 
  {
    printf "$i\n"
    ++$i
  } goto continue
  label break:
}  


template "for"(init; cond; inc)[continue, break]{algo}{
  expr init
  label continue: if(expr !cond) goto break 
  {
    {algo}
    expr inc
  } goto continue
  label break:
}  

for($i=0; $i<10; ++$i) {
  printf "$i\n"
}

```

But like in C++ this one above is a runtime loop!
A Meta-Loop looks like this:


```c4

func<0> =>> func<0>(): false
func<n> =>> func<n>(): func<n-1>

func<int x>() => print "$x\n"

func<10>()

```

will be evaluated to ...

```c4

func<10>() ->  func<x:10> { print "10\n"; func<x:9>{ print "9\n"; func<x:8>{ print "8\n"; func<x:7>{ 

```

