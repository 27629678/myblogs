
### 1、greadlink

####- Description:

Print value of a symbolic link or canonical file name

\-f, \-\-canonicalize

canonicalize  by  following  every  symlink  in every component of the given name recur-sively; all but the last component must exist
             

```
$greadlink -f file.md		=> "Users/$/.../file.md"
```