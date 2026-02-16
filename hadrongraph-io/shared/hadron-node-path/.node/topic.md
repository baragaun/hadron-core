A Hadron node's `path` has this format:

```
<domain>[.project][.module][.submodule][].<node-name>
```

The path maps to a directory containing `.node/node.yaml`:

```
.hadron/<domain>/[<project>/][<module>/]<node-name>/.node/node.yaml
```
