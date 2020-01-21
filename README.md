# C4
A Macro Language with template engine function and influence from m4, awk, ruby, razor, cpp (c preprocessor) and perl

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
match(/^## $*$/): head2($*);

begin() {
  $h2_counter = 0;
}

head2(mark content) {
  <h2>@++$h2_counter. content</h2>
}
```

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
 match(/^# $*$/): head1($*);
match(/^## $*$/): head2($*);

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


