假设分支为
``` plaintext
main: A -> B -> C
             \
dev:          D -> E
```
merge之后
``` plaintext
main: A -> B -> C ------> M  (M是合并提交)
             \           /
dev:          D -> E ----
```
rebase后
``` plaintext
main: A -> B -> C -> D' -> E'
```
