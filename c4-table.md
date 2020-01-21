

# a Table with c4

### A little more complex c4 Macro Definition:
which will reset the counter by each parent heading (h1):

```c4
#!/usr/bin/c4 -w
#mdtable.c4

$headline="|?( *$@ *|!)+";
$sepline= "|?( *:?{@isLeft[loop]}---+:?{@isRight[loop]} *|)+{@loop}";
$cell="(|.*|?)|(.*|)";

m = match(/^$headline$^$sepline$^/):=> table($@, match($context, /$cells/), m);


table([] columns,() macro, var matches) {
  align = (int n){ 
    if(matches.isLeft[n] && matches.isRight[n]) {
      return "CellCenter";
    } else if(matches.isRight[n]) {
      return "CellRight";
    } else {
      return "CellLeft";
    }
  };
    
  <table>
    <tr>
  columns.each(c) {
      <th>@c</th>
  }
    </tr>
  @while(f = macro.parse()) {
    if(f.contains('\n')) c=0;
    if(c==0) {
    <tr>  
    }
      <td class="@align(++c)">@f</td>
    if(c==$@.length) {
    </tr>  
    }
  }
  </table>
}
```

### The Markdown:
```md
aaaa | bbb | ccc
 ---:  | :---:  | :---
 a |  
 a | b | c  | d
 a | b 
```

### The c4 HTML output from Markdown:
```html
<table>
<thead>
 <tr>
  <th align="right">aaaa</th>
  <th align="center">bbb</th>
  <th align="left">ccc</th>
 </tr>
</thead>
<tbody>
 <tr>
  <td align="right">a</td>
  <td align="center"></td>
  <td align="left"></td>
 </tr>
 <tr>
  <td align="right">a</td>
  <td align="center">b</td>
  <td align="left">c</td>
 </tr>
 <tr>
  <td align="right">a</td>
  <td align="center">b</td>
  <td align="left"></td>
 </tr>
</tbody>
</table>
```

