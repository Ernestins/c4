

# a Table with c4

### A little more complex c4 Macro Definition:
which will reset the counter by each parent heading (h1):

```c4
#!/usr/bin/c4 -w
#mdtable.c4

$headline="|?( *$@ *|!)+";
$sepline= "|?( *:?{@isLeft[loop]}---+:?{@isRight[loop]} *|)+{@loop}";
$cell="(|.*|?)|(.*|)";

 match(/^$headline$^$sepline$^/):=> table($@, match(/$cells), );
match(/^## $*$/):=> head2($*);

begin() {
  $h1_counter = 0;
  $h2_counter = 0;
}

align(

table([] columns,() macro, var matches) {
  <table>
    <tr>
  columns.each(c) {
      <th>@c</th>
  }
    </tr>
  @while(f = macro.parse()) {
    if(c==0) {
    <tr>  
    }
      <td class="@align(++c)">@f</td>
    if(c==$@.length) {
    </tr>  
    }
   </table>
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
