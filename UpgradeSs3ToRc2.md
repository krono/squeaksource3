
```
#!/bin/bash
if [ -d /etc/default/gemstone ]; then
        . /etc/default/gemstone
fi

if [ -s $GEMSTONE/seaside/etc/gemstone.secret ]; then
    . $GEMSTONE/seaside/etc/gemstone.secret
else
    echo 'Missing password file $GEMSTONE/seaside/etc/gemstone.secret'
    exit 1
fi

echo "Starting update: " `date` >> $GEMSTONE_LOGDIR/update.log

cat << EOF | $GEMSTONE/bin/topaz -l -T50000 >> $GEMSTONE_LOGDIR/update.log 2>&1 

set user DataCurator pass $GEMSTONE_CURATOR_PASS gems $GEMSTONE_NAME

display oops
iferr 1 where
iferr 2 stack
iferr 3 exit 1

login

run
(ObjectLogEntry trace: 'UPDATE: begin ' object: 'Install tODE') addToLog.
%   
commit 

run
ConfigurationOfGLASS project updateProject.
ConfigurationOfGsMonticello project updateProject.
ConfigurationOftODE project updateProject.
ConfigurationOfMetacello project updateProject.
ConfigurationOfSeaside30 project updateProject.
ConfigurationOfSqueakSource project updateProject.
ConfigurationOfJQueryWidgetBox project updateProject.
%

commit

run
GsDeployer deploy: [
        (ConfigurationOfMetacello project version: '1.0-beta.31.1') load.
        (ConfigurationOfGLASS project version: '1.0-beta.8.7') load.    
].
%

commit

run
GsDeployer bulkMigrate: [
        (ConfigurationOfSeaside30 project version: '3.0.5.0.1') load: #('Base' 'JQuery-UI' 'Seaside-Adaptors-Swazoo' ).
        (ConfigurationOfJQueryWidgetBox project version: '2.4') load: #('JQWidgetBox-UILayout-Core' 'JQWidgetBox-Splitter-Core' ).
        (ConfigurationOftODE project version: '0.1') load.
].
%
commit

run
(ObjectLogEntry trace: 'UPDATE: completed' object: 'Install tODE') addToLog.
%   
commit 


logout
quit

EOF

echo "Finished update: " `date` >> $GEMSTONE_LOGDIR/update.log
echo "------------------------------------------------------------" >> $GEMSTONE_LOGDIR/update.log
```