![banner](./.github/banner.png)

<p align="center">
  A simple way to read and write LAPS passwords from linux.
  <br>
  
  <img alt="GitHub release (latest by date)" src="https://img.shields.io/github/v/release/p0dalirius/pyLAPS">
  <a href="https://twitter.com/intent/follow?screen_name=podalirius_" title="Follow"><img src="https://img.shields.io/twitter/follow/podalirius_?label=Podalirius&style=social"></a>
  <br>
</p>

This script is a python setter/getter for property ms-Mcs-AdmPwd used by LAPS inspired by [@swisskyrepo](https://github.com/swisskyrepo/)'s [SharpLAPS](https://github.com/swisskyrepo/SharpLAPS) in C#.

Require (either):
  * Account with `ExtendedRight` or `GenericRead` to get LAPS passwords
  * Account with `ExtendedRight` or `GenericWrite` to set LAPS passwords
  * Domain Admin privileges

## General usage

This tool supports many authentication methods:

```
                 __    ___    ____  _____
    ____  __  __/ /   /   |  / __ \/ ___/
   / __ \/ / / / /   / /| | / /_/ /\__ \   
  / /_/ / /_/ / /___/ ___ |/ ____/___/ /   
 / .___/\__, /_____/_/  |_/_/    /____/    v1.2
/_/    /____/           @podalirius_           

usage: pyLAPS.py [-h] [--use-ldaps] [-q] [-debug] [-a [{get,set}]] [-c TARGET_COMPUTER] [-v TARGET_VALUE] [--dc-ip ip address] [-d DOMAIN] [-u USER]
 [--no-pass | -p PASSWORD | -H [LMHASH:]NTHASH | --aes-key hex key] [-k]

Python setter/getter for property ms-Mcs-AdmPwd used by LAPS.

optional arguments:
-h, --help            show this help message and exit
--use-ldaps           Use LDAPS instead of LDAP
-debug                Debug mode
-a [{get,set}], --action [{get,set}]
        Get or Set the LAPS password
-c TARGET_COMPUTER, --computer TARGET_COMPUTER
        Target computer to modify
-v TARGET_VALUE, --value TARGET_VALUE
        New password to set

authentication & connection:
--dc-ip ip address    IP Address of the domain controller or KDC (Key Distribution Center) for Kerberos. If omitted it will use the domain part (FQDN)
        specified in the identity parameter
-d DOMAIN, --domain DOMAIN
        (FQDN) domain to authenticate to
-u USER, --user USER  User to authenticate as

--no-pass             don't ask for password (useful for -k)
-p PASSWORD, --password PASSWORD
        password to authenticate with
-H [LMHASH:]NTHASH, --hashes [LMHASH:]NTHASH
        NT/LM hashes, format is LMhash:NThash
--aes-key hex key     AES key to use for Kerberos Authentication (128 or 256 bits)
-k, --kerberos        Use Kerberos authentication. Grabs credentials from .ccache file (KRB5CCNAME) based on target parameters. If valid credentials cannot be found, it will use the ones specified in the command line

```

## Read the password of all computers

```
# ./pyLAPS.py --action get -u 'Administrator' -d 'LAB.local' -p 'Admin123!' --dc-ip 192.168.2.1
                 __    ___    ____  _____
    ____  __  __/ /   /   |  / __ \/ ___/
   / __ \/ / / / /   / /| | / /_/ /\__ \   
  / /_/ / /_/ / /___/ ___ |/ ____/___/ /   
 / .___/\__, /_____/_/  |_/_/    /____/    v1.2
/_/    /____/           @podalirius_           

[+] Extracting LAPS passwords of all computers ...
  | PC01$           : H0zqUkoCAcbVXf0Q3ZbQ
  | PC02$           : PoSaRYUhqbLQjWjY9D0g
  | PC17$           : spf41mUjm9pxrgl11Osd
  | PC25$           : oqy814nS1p2lRVlatcKG
```


## Read the password of a specific computer

```
# ./pyLAPS.py --action get --computer 'PC01$' -u 'Administrator' -d 'LAB.local' -p 'Admin123!' --dc-ip 192.168.2.1
                 __    ___    ____  _____
    ____  __  __/ /   /   |  / __ \/ ___/
   / __ \/ / / / /   / /| | / /_/ /\__ \   
  / /_/ / /_/ / /___/ ___ |/ ____/___/ /   
 / .___/\__, /_____/_/  |_/_/    /____/    v1.2
/_/    /____/           @podalirius_           

[+] Extracting LAPS password of computer: PC01$ ...
[+] Searching for the target computer: PC01$
[+] Target computer found: CN=PC01,OU=LAPS Managed Computers,DC=LAB,DC=local
  | PC01$           : Wookie123
```

## Write a random password to a specific computer

When no password is supplied with `--value`, a random password is generated.

```
# ./pyLAPS.py --action set --computer 'PC01$' -u 'Administrator' -d 'LAB.local' -p 'Admin123!' --dc-ip 192.168.2.1
                 __    ___    ____  _____
    ____  __  __/ /   /   |  / __ \/ ___/
   / __ \/ / / / /   / /| | / /_/ /\__ \   
  / /_/ / /_/ / /___/ ___ |/ ____/___/ /   
 / .___/\__, /_____/_/  |_/_/    /____/    v1.2
/_/    /____/           @podalirius_           

[+] Setting LAPS password in LDAP ...
[+] Searching for the target computer
[+] Target computer found: CN=PC01,OU=LAPS Managed Computers,DC=LAB,DC=local
[+] Using random password: qnaEmUsWOqQ6d2PR6wVr
[+] Successfully updated the ms-Mcs-AdmPwd attribute of the target PC01$ to qnaEmUsWOqQ6d2PR6wVr
```

## Write a specific password to a specific computer

You can set the value of `ms-Mcs-AdmPwd` on a specific computer with `--value` option like this:

```
# ./pyLAPS.py --action set --computer 'PC01$' --value "Wookie123" -u 'Administrator' -d 'LAB.local' -p 'Admin123!' --dc-ip 192.168.2.1
                 __    ___    ____  _____
    ____  __  __/ /   /   |  / __ \/ ___/
   / __ \/ / / / /   / /| | / /_/ /\__ \   
  / /_/ / /_/ / /___/ ___ |/ ____/___/ /   
 / .___/\__, /_____/_/  |_/_/    /____/    v1.2
/_/    /____/           @podalirius_               

[+] Setting LAPS password in LDAP ...
[+] Searching for the target computer
[+] Target computer found: CN=PC01,OU=LAPS Managed Computers,DC=LAB,DC=local
[+] Successfully updated the ms-Mcs-AdmPwd attribute of the target PC01$ to Wookie123
```

## Contributing

Pull requests are welcome. Feel free to open an issue if you want to add other features.
