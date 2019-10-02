
```
alias rm="sh /sweeat/tools/remove.sh"
```

```
TRASH_DIR="/sweeat/.trash"  
  
for i in $*; do  
    STAMP=`date "+%Y%m%d-%H%M%S.%s"`  
    fileName=`basename $i`  
    mv $i $TRASH_DIR/$fileName.$STAMP  
done
```