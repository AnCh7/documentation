## Useful

Update all projects

```bash
#!/bin/bash
for i in */.git
do
	(echo ------------ $i ------------; 
    cd $i/..; 
    git pull; 
    git config core.filemode false;) &
done
wait
```

