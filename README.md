# C4
A Macro Language with template engine function and influence from m4, awk, ruby, razor and perl

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

### The c4 Macro Definition:
```c4
 match(/^# $*$/): head1($*);
match(/^## $*$/): head2($*);

begin() {
  $h2_counter = 0;
}

head1(mark content) {
  $h2_counter = 0;  
  <h1>content</h1>
}

head2(mark content) {
  <h2>@++$h2_counter. content</h2>
}
```
