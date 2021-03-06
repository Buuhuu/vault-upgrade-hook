# Sample Package

## Requirements

- `org.apache.sling.pipes:0.0.10` requires `org.apache.sling.api.resource,version=[2.8,3)`. So it will not work in AEM 6.0 SP3 (which provides org.apache.sling.api.resource,version=2.6.0) 

## create package with hook

Copy `vault-upgrade-hook-0.0.1-SNAPSHOT.jar` to `META-INF/vault/hooks`
Place your scripts to `META-INF/vault/definition/upgrader/foo`
Package to a zip like this:
    
    $ cd sling-pipes-sample-package
    $ zip --filesync -r ../sling-pipes-sample-package.zip *

Upload to AEM and install. Notice the scripts placed to `META-INF/vault/definition/upgrader/foo` are executed.

The sample package has scripts for PREPARE, INSTALLED and END phase as samples in `META-INF/vault/definition/upgrader/test-sling-pipes`

## upload and install package - with output sample

For a quick turnaround you can install via cUrl like this

    $ curl -X POST -F"file=@../sling-pipes-sample-package.zip" -F"install=true" -uadmin:admin localhost:4502/crx/packmgr/service.jsp

Notice output:

    ...
    Installing content
    Creating snapshot for package netcentric:sling-pipes-sample-package:0.0.1-SNAPSHOT 
    A META-INF
    A META-INF/vault
    A META-INF/vault/config.xml
    A META-INF/vault/filter.xml
    A META-INF/vault/nodetypes.cnd
    A META-INF/vault/properties.xml
    A /.content.xml
    A META-INF/vault/definition/.content.xml
    Executing content upgrade in phase PREPARE 
    Content version: 0.0.0 
    Package version: 0.0.1-SNAPSHOT 
    H Executing upgrade: Update via Sling Pipe Handler. - version 0.0.1-SNAPSHOT
    I Found sling pipe at /etc/packages/netcentric/sling-pipes-sample-package-0.0.1-SNAPSHOT.zip/jcr:content/vlt:definition/upgrader/test-sling-pipes/installed_pipe
    I Found sling pipe at /etc/packages/netcentric/sling-pipes-sample-package-0.0.1-SNAPSHOT.zip/jcr:content/vlt:definition/upgrader/test-sling-pipes/end_pipe
    I Found sling pipe at /etc/packages/netcentric/sling-pipes-sample-package-0.0.1-SNAPSHOT.zip/jcr:content/vlt:definition/upgrader/test-sling-pipes/prepare_pipe
    I Found sling pipe at /etc/packages/netcentric/sling-pipes-sample-package-0.0.1-SNAPSHOT.zip/jcr:content/vlt:definition/upgrader/test-sling-pipes/failed_pipe
    I Running sling pipe at prepare_pipe
    I Executing prepare_pipe
    I /content/geometrixx/fr/products
    I /content/geometrixx/fr/services
    ...
    
    Package installed in 178ms.

## Versioning

[JsonResourceSerializer](https://github.com/Netcentric/vault-upgrade-hook/blob/develop/vault-upgrade-hook/src/main/java/biz/netcentric/vlt/upgrade/util/JsonResourceSerializer.java) is used to serialize the whole sling pipe's resource tree to String. Then it's used to generate md5 hash which is used as its version. 
The sample package has no `mode` specified what is equal to `mode=on_change`. With such configuration version check would prevent the pipes from being executed multiple times (when no changes applied). Option `run=always` will force execution of them every package installation.