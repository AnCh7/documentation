## Useful

Updated all repos in current directory

```bash
#!/bin/bash

process() {
	echo ----------------------------;
	cd $1/..;
	pwd;

	# WILL RESET ALL
	git fetch --all;
	git reset --hard origin/master;
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

