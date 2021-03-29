## Useful

Updated all repos in current directory

```bash
#!/bin/bash

process() {
	echo ----------------------------;
	cd $1/..;
	pwd;
	# track all remote branches
	git branch -r | grep -v '\->' | while read remote; do git branch --track "${remote#origin/}" "$remote"; done;
	# update local branches which track remote branches
	git fetch --all;
	git pull --all;
	echo;
	git config core.filemode false;

	cd ../..;
}

N=4
(
for each_dir in */.git; do 
   # PARALLEL
   ((i=i%N)); ((i++==0)) && wait
   process "$each_dir" & 
done
)
```

