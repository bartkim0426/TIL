# 4 spaces to 2 spaces in vim

```
// 4 spaces to 2 spaces
%s;^\(\s\+\);\=repeat(' ', len(submatch(0))/2);g

// Tab to 2 spaces
:%s/\t/  /g
```
