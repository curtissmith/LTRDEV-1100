Navigation :: [Previous Page](LTRPRG-1100-03d2-GuestShell-Ex1.md) :: [Table of Contents](LTRPRG-1100-00-Intro.md#table-of-contents) :: [Next Page](LTRPRG-1100-04-Test.md)

---

### Exercise 2: Unleashing Network Programmability at the Network Edge

#### Objectives

The objectives for this exercise are to:

* Learn how to use the CLI Python package to run IOS XE EXEC and configuration mode commands

#### Step 1: Running IOS XE Commands with the CLI Python Package

Cisco packages a Python module with Guest Shell that provides access to run IOS XE EXEC and configuration commands.  
This is called Cisco IOS XE Python API.  The name of the Python module is cli.  The cli Python module is not 
available for use outside of IOS XE Guest Shell.

In additional to configuring and enabling Guest Shell, we must complete come basic IOS XE configuration steps on the 
network device for IOS XE Python API to function.

1. Establish an SSH connection to the IOS XE device `csr1` by double clicking the CSR1 PuTTY icon on the desktop:
    
    ![CSR1 PuTTY Icon](assets/CSR1-Icon.png)
    
    ![CSR1 Terminal](assets/CSR1-Term.png)

2. From the IOS XE device CLI, ensure you are in privileged EXEC mode as indicated by the `csr1#` prompt.  If you are
in user EXEC mode as indicated by the `csr1>` prompt, then enter privileged EXEC mode with the `enable` command, for
example:
    
    ```
    csr1>enable
    csr1#
    ```

3. Enter global configuration mode, which will be indicated by the `csr1(config)#` prompt, with the
`configure terminal` command, for example:
    
    ```
    csr1#configure terminal
    Enter configuration commands, one per line.  End with CNTL/Z.
    csr1(config)#
    ```

4. Ensure the `ip http server` command is enabled:
    
    ```
    csr1(config)#ip http server
    csr1(config)#
    ```
    
    End the global configuration mode session with the `end` command or typing `CTRL-Z`, for example:
    
    ```
    csr1(config)#end
    csr1#
    ```

5. Enter a Guest Shell interactive session with the IOS XE command `guestshell run bash`, for example:
    
    ```
    csr1#guestshell run bash
    [guestshell@guestshell ~]$

6. The cli Python module has six functions that can execute IOS XE CLI commands.  To use these functions, use 
`import cli` in a Python script or the Python interpreter.  For example, run the `python` command from the Guest Shell 
`[guestshell@guestshell ~]$` prompt:
    
    ```
    [guestshell@guestshell ~]$ python
    Python 2.7.5 (default, Jun 17 2014, 18:11:42)
    [GCC 4.8.2 20140120 (Red Hat 4.8.2-16)] on linux2
    Type "help", "copyright", "credits" or "license" for more information.
    >>>
    ```
    
    Type the code snippet `import cli`, for example:
    
    ```
    >>> import cli
    >>>
    ```
    
    To execute an IOS XE CLI command from a Python script or Python interpreter, enter the CLI command as an argument 
    string to one of the following six functions:
    
    * `cli()` - This function takes an IOS XE command as an argument, runs the command through the IOSparser, and 
    returns the resulting text.  If this command is malformed, a Python exception is raised.  Try a `cli()` function 
    call with the following code snippet:
        
        ```
        result=cli.cli("show ip interface brief")
        print(result)
        ```
        
        For example:
        
        ```
        >>> result=cli.cli("show ip interface brief")
        >>> print(result)
        Interface              IP-Address      OK? Method Status                Protocol
        GigabitEthernet1       198.18.134.11   YES NVRAM  up                    up      
        VirtualPortGroup0      192.168.35.1    YES manual up                    up
        ```
    
    * `clip()` - This function works exactly the same as the `cli()` function, except that it prints
    the resulting text to stdout rather than returning it.  Try a `clip()` function call with the following code 
    snippet:
        
        ```
        cli.clip("show ip interface brief")
        ```
        
        For example:
        
        ```
        >>> cli.clip("show ip interface brief")
        
        Interface              IP-Address      OK? Method Status                Protocol
        GigabitEthernet1       198.18.134.11   YES NVRAM  up                    up
        VirtualPortGroup0      192.168.35.1    YES manual up                    up
        
        >>>
        ```
    
    * `execute()` - This function executes a single IOS XE EXEC mode command and returns the resulting text.  
    You must use a Python list with a loop to execute this function more than once.  Try an `execute()` function call 
    with the following code snippet:
        
        ```
        result=cli.execute("show ip interface brief")
        print(result)
        ```
        
        For example:
        
        ```
        >>> result=cli.execute("show ip interface brief")
        >>> print(result)
        Interface              IP-Address      OK? Method Status                Protocol
        GigabitEthernet1       198.18.134.11   YES NVRAM  up                    up      
        VirtualPortGroup0      192.168.35.1    YES manual up                    up
        >>>
        ```
    
    * `executep()` - This function executes a single IOS XE command and prints the resulting text to stdout
    rather than returning it.  Try an `executep()` function call with the following code snippet:
        
        ```
        cli.executep("show ip interface brief")
        ```
        
        For example:
        
        ```
        >>> cli.executep("show ip interface brief")
        Interface              IP-Address      OK? Method Status                Protocol
        GigabitEthernet1       198.18.134.11   YES NVRAM  up                    up
        VirtualPortGroup0      192.168.35.1    YES manual up                    up
        >>>
        ```
    
    * `configure()` - This function configures the network device with the configuration passed to it.  It 
    returns a Python list of named tuples that contains each command and its result in the general format:
        
        ```
        [ConfigResult: result = (bool(success), original_command, error_information)]
        ```
        
        Try a `configure()` function call with the following code snippet:
        
        ```
        result=cli.configure(["interface Loopback1", "ip address 10.1.1.1 255.255.255.0", "no shutdown"])
        print(result)
        ```
        
        For example:
        
        ```
        >>> result=cli.configure(["interface Loopback1", "ip address 10.1.1.1 255.255.255.0", "no shutdown"])
        >>> print(result)
        [ConfigResult(success=True, command='interface Loopback1', line=1, output='', notes=None),
        ConfigResult(success=True, command='ip address 10.1.1.1 255.255.255.0', line=2, output='',
        notes=None), ConfigResult(success=True, command='no shutdown', line=3, output='', notes=None)]
        >>>
        ```
    
    * `configurep()` - This function works exactly the same as the `configure()` function, except that it prints the 
    resulting text to stdout rather than returning it.  Try a `configure()` function call with the following code 
    snippet:
        
        ```
        cli.configurep(["interface Loopback1", "ip address 10.1.1.1 255.255.255.0", "no shutdown"])
        ```
        
        ```
        >>> cli.configurep(["interface Loopback1", "ip address 10.1.1.1 255.255.255.0", "no shutdown"])
        Line 1 SUCCESS: interface Loopback1
        Line 2 SUCCESS: ip address 10.1.1.1 255.255.255.0
        Line 3 SUCCESS: no shutdown
        >>>
        ```
    
    You can get help and display the details of each function by using the `help()` function in the Python 
    interpreter.  For example, for help with the `configurep()` function, use a function call like
    `help(cli.configurep)` in the Python interpreter, for example:
    
    ```
    >>> help(cli.configurep)
    Help on function configurep in module cli:
    
    configurep(configuration)
        Apply a configuration (set of Cisco IOS CLI config-mode commands) to the dev
    ice
        and prints the result.
    
        configuration = '''interface gigabitEthernet 0/0
                             no shutdown'''
    
        # push it through the Cisco IOS CLI.
        configurep(configuration)
    
        Args:
            configuration (str or iterable): Configuration commands, separated by ne
    wlines.
    
    >>>
    ```
    
    For general help with the CLI Python package itself rather than a specific function, use the `help(cli)` function
    call in the Python interpreter, for example (output truncated for brevity):
    
    ```
    >>> help(cli)
    Help on package cli:
    
    NAME
        cli - Run Cisco IOS CLI commands and receive the output.
    
    FILE
        /usr/lib/python2.7/site-packages/cli/__init__.py
    
    DESCRIPTION
        This module contains utility functions that run Cisco IOS CLI commands and p
    rovide
        the output.
    
    PACKAGE CONTENTS
        _test (package)
    
    CLASSES
        ConfigResult(__builtin__.tuple)
            ConfigResult
        exceptions.Exception(exceptions.BaseException)
            CLIError
                CLICommandError
                    CLISyntaxError
    --More--
    ```
    
    Type the `q` key to exit out of the help text at the `--More--` prompt.

7. End the Python interpreter session with the `quit()` function:
    
    ```
    >>> quit()
    [guestshell@guestshell scripts]$

    ```

8. There is an example Python script `iosxe-cli-example.py` in this lab's Git repository.  Let's create a directory on 
the network device `bootflash:`, transfer the file to the network device file system, and run the script.
    
    To create a new directory to hold scripts on-box if one does not already exist, use the `mkdir -p /bootflash/scripts` 
    command from the Guest Shell `[guestshell@guestshell ~]$` prompt, for example:
    
    ```
    [guestshell@guestshell ~]$ mkdir -p /bootflash/scripts
    [guestshell@guestshell ~]$
    ```
    
    Change to that directory with the `cd /bootflash/scripts` command, for example:
    
    ```
    [guestshell@guestshell ~]$ cd /bootflash/scripts
    [guestshell@guestshell scripts]$
    ```
    
    Transfer the example script with the `wget https://raw.githubusercontent.com/CiscoSE/LTRPRG-1100/master/code/iosxe-cli-example.py` command, for example:
        
    ```
    [guestshell@guestshell scripts]$ wget https://raw.githubusercontent.com/CiscoSE/LTRPRG-1100/master/code/iosxe-cli-example.py
    --2019-05-31 19:42:45--  https://raw.githubusercontent.com/CiscoSE/LTRPRG-1100/master/code/iosxe-cli-example.py
    Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 151.101.64.133, 151.101.128.133, 151.101.192.133, ...
    Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|151.101.64.133|:443... connected.
    HTTP request sent, awaiting response... 200 OK
    Length: 1604 (1.6K) [text/plain]
    Saving to: 'iosxe-cli-example.py'
    
    100%[======================================>] 1,604       --.-K/s   in 0s
    
    2019-05-31 19:42:45 (81.9 MB/s) - 'iosxe-cli-example.py' saved [1604/1604]
    
    [guestshell@guestshell scripts]$
    ```
    
    This example Python script requires a single mandatory argument: an interface name, for example `Loopback55`.  Run 
    the script with the `python iosxe-cli-example.py Loopback55` command:
    
    ```
    [guestshell@guestshell scripts]$ python iosxe-cli-example.py Loopback55
    
    Configuring interface Loopback55 with 'configurep' function...
    
    Line 1 SUCCESS: interface Loopback55
    Line 2 SUCCESS: ip address 10.55.55.55 255.255.255.0
    Line 3 SUCCESS: no shut
    Line 4 SUCCESS: end
    
    Configuring interface Loopback55 with 'configure' function...
    
    Printing show command output with 'executep' function...
    
    Interface              IP-Address      OK? Method Status                Protocol
    GigabitEthernet1       10.0.2.15       YES DHCP   up                    up      
    Loopback55             10.55.55.55     YES TFTP   up                    up      
    VirtualPortGroup0      192.168.35.1    YES NVRAM  up                    up      
    
    Printing show command with 'execute' function...
    
    Building configuration...
    Current configuration : 93 bytes
    !
    interface Loopback55
     ip address 10.55.55.55 255.255.255.0
     logging event link-status
    end
    
    Configuring interface Loopback55 with 'cli' function...
    
    Printing show command with 'clip' function...
    
    Building configuration...
    Current configuration : 155 bytes
    !
    interface Loopback55
     description Configured with a Python script from Guest Shell
     ip address 10.55.55.55 255.255.255.0
     logging event link-status
    end
    
    [guestshell@guestshell scripts]$ 
    ```

This is an introduction to the IOS XE Python API and the Python CLI library packaged with Guest Shell.  We will use 
the power of on-box Python with Python API throughout the rest of this lab, so you will see more real-world use cases.

We've armed you with the tools of the ninja and began to hone the skills of the ninja.  Now let's start putting this 
network programmability training to test.

---

Navigation :: [Previous Page](LTRPRG-1100-03d2-GuestShell-Ex1.md) :: [Table of Contents](LTRPRG-1100-00-Intro.md#table-of-contents) :: [Next Page](LTRPRG-1100-04-Test.md)
