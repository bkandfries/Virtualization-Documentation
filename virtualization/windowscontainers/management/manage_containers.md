# Container Management

The container life cycle includes actions such as, starting a container, stopping a container, and removing a container. While performing these actions you may also need retrieve a list of container images, manage container networking, and container resources. This document will detail basic container management tasks using PowerShell.

## Container Operations PowerShell

### Create a Container

When creating a new container, you will need the name of a container image that will serve as the container base. This can be found using the `Get-ContainerImageName` command.

```powershell
PS C:\> Get-ContainerImage
Name              Publisher    Version         IsOSImage
----              ---------    -------         ---------
NanoServer        CN=Microsoft 10.0.10584.1000 True
WindowsServerCore CN=Microsoft 10.0.10584.1000 True
```

Use the `New-Container` command to create a new container. When a container is created with PowerShell the associated files are stored under `c:\ProgramData\Microsoft\Windows\Hyper-C\Vontainer Image Store`.

```powershell
PS C:\> New-Container -Name TST -ContainerImageName WindowsServerCore
Name State Uptime   ParentImageName
---- ----- ------   ---------------
TST  Off   00:00:00 WindowsServerCore
```

Once the container has been created, add a network adapter to the container.

```powershell
PS C:\> Add-ContainerNetworkAdapter -ContainerName TST
```

Connet the containers network adapter to a virtuial switch. To get the name of the virtual switch use the `Get-VMSwitch` command.

```powershell
PS C:\> Get-VMSwitch
Name SwitchType NetAdapterInterfaceDescription
---- ---------- ------------------------------
DHCP External   Microsoft Hyper-V Network Adapter
NAT  NAT
```

Connect the network adapter the virtual switch using `Connect-ContainerNetowkrAdapter`. NOTE – this can also be completed when the container is crated using the –SwitchName parameter.

```powershell
PS C:\> Connect-ContainerNetworkAdapter -ContainerName TST -SwitchName NAT
```

### Start a Container
In order to start the container, a PowerShell object representing that container that will be enumerated. This can be done by placing the output of `Get-Container` into a PowerShell variable.

```powershell
PS C:\> $container = Get-Container -Name TST

```
This can then be used with the `Start-Container` command to start the container.
```powershell
PS C:\> Start-Container $container
```

The following will start all containers on the host.

```powershell
PS C:\> Get-Container | Start-Container
```

### Connect with Container

PowerShell remoting can be used to connect to a container. This may be helpful if you need to manually perform a task such as installing software, starting a processes or troubleshooting a container.
To create an interactive session with the container, use the `Enter-PSSession` command.

 ```powershell
PS C:\> Enter-PSSession -ContainerName TST –RunAsAdministrator
```

Notice that once the remote PowerShell session has been created that the shell prompt reflects the container name.

```powershell
[TST]: PS C:\>
```

Commands can also be run against a container without creating a persistent PowerShell session using the `Invoke-Command` command.
The following sample creates a folder named ‘Application’ in the container.

```powershell

PS C:\> Invoke-Command -ContainerName TST -ScriptBlock {New-Item -ItemType Directory -Path c:\application }
 Directory: C:\
Mode                LastWriteTime         Length Name                                                 PSComputerName
----                -------------         ------ ----                                                 --------------
d-----       10/28/2015   3:31 PM                application                                          TST
```

### Stop a Container

In order to stop the container a PowerShell object representing that container will be stopped. This can be done by placing the output of `Get-Container` into a PowerShell variable.

```powershell
PS C:\> $container = Get-Container -Name TST

```
This can then be used with the `Stop-Container` command to stop the container.
```powershell
PS C:\> Stop-Container $container
```

The following will stop all containers on the host.

```powershell
PS C:\> Get-Container | Stop-Container
```

### Remove a Container

When a container is no longer needed it can be removed. In order to remove a container, it needs to be in a stopped state and a PowerShell object needs to be created that represents the container. This can be done using the **Get-Container** command.

```powershell
PS C:\> $container = Get-Container -Name TST
```

To remove the container, use the `Remove-Container` command.

```powershell
PS C:\> Remove-Container $container -Force
```

The following will remove all containers on the host assuming they are in a stopped state.

```powershell
PS C:\> Get-Container | Remove-Container -Force
```

## Container Operations Docker

### Create a Container
### Start a Container
### Connect with Container
### Stop a Container
### Remove a Container

### Additional Resources

For a complete reference of Container PowerShell commands see the [Container PowerShell Reference](https://technet.microsoft.com/en-us/library/mt433069.aspx).

For documentation on managing Windows Containers with Docker see the Docker document [Working with Containers]( https://docs.docker.com/userguide/usingdocker/).