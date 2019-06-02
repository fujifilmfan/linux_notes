Chapter 33: Pluggable Authentication Modules (PAM)
--------------------------------------------------

[33.3: Learning Objectives](#333-learning-objectives)  
[33.4: PAM: A Unified Approach to Authentication](#334-pam-a-unified-approach-to-authentication)  
[33.5: Authentication Process](#335-authentication-process)  
[33.6: PAM Configuration Files](#336-pam-configuration-files)  
[33.7: PAM Rules](#337-pam-rules)  
[33.8: LDAP Authentication](#338-ldap-authentication)  
[Lab 33.1: PAM Configuration](#lab-331-pam-configuration)  
[Paths and Commands](#paths-and-commands)  
  
### 33.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Explain the basic concepts that motivate the use of PAM.
* List the steps involved in the authentication process.
* Use and modify PAM configuration files.
* Know how to interpret PAM rules and create new ones.
* Apply LDAP to use and administer distributed directory services over the network.
  
### 33.4: PAM: A Unified Approach to Authentication
----
* **PAM** (**P**luggable **A**uthentication **M**odules) allow applications to handle authentication in one, uniform way, using **libpam**
* **libpam** provides flexibility and consistency wrt authentication, password, session, and account services
* PAM incorporates:
    * PAM-aware applications
    * configuration files in `/etc/pam.d/` (one for each PAM-aware application or service)
    * PAM modules in the **libpam\*** libraries 
  
### 33.5: Authentication Process
----
Steps involved in authentication:  
* a user invokes a PAM-aware application, like **login**, **ssh**, or **su**
* the application calls **libpam**
* the library checks for files in `/etc/pam.d`
* these files delineate with PAM modules to invoke, including **system-auth**
* each referenced module is executed in accordance with the rules of the relevant configuration file for that application
  
### 33.6: PAM Configuration Files
----
* each file in `/etc/pam.d` corresponds to a service
* each line in the file specifies a rule, formatted as a list of space-separated tokens; the first two tokens are case-insensitive
* lines take the following form: **TYPE CONTROL MODULE-PATH MODULE-ARGUMENTS**
* examples:
    * `$ cat /etc/pam.d/su` on my Mac
        ```
        # su: auth account session
        auth       sufficient     pam_rootok.so 
        auth       required       pam_opendirectory.so
        account    required       pam_group.so no_warn group=admin,wheel ruser root_only fail_safe
        account    required       pam_opendirectory.so no_check_shell
        password   required       pam_opendirectory.so
        session    required       pam_launchd.so
        ```
    * `$ cat /etc/pam.d/su` on CentOS 7
        ```
        #%PAM-1.0
        auth		sufficient	pam_rootok.so
        # Uncomment the following line to implicitly trust users in the "wheel" group.
        #auth		sufficient	pam_wheel.so trust use_uid
        # Uncomment the following line to require a user to be in the "wheel" group.
        #auth		required	pam_wheel.so use_uid
        auth		substack	system-auth
        auth		include		postlogin
        account		sufficient	pam_succeed_if.so uid = 0 use_uid quiet
        account		include		system-auth
        password	include		system-auth
        session		include		system-auth
        session		include		postlogin
        session		optional	pam_xauth.so
        ```
  
### 33.7: PAM Rules
----
* **TYPE** specifies the **management group** to which to associate the module
    * **auth**: instructs app to prompt user for identification; may set credentials and grant privileges
    * **account**: checks on aspects of the user's account, such as password aging, access control, etc.
    * **password**: responsibile for updating the user authentication token, usually a password
    * **session**: used to provide functions before and after the session is established (such as setting up environment, logging, etc.)
* **CONTROL** controls how the success or failure of a module affects the overall authentication process
    * **required**: must return success for the service to be granted; if part of a stack, all other modules are still executed; application is not told which module or modules failed
    * **requisite**: same as **required**, except a failure in any module terminates the stack and a return status is sent to the application
    * **optional**: module is not required; if it is the only module, then its return status to application may cause failure
    * **sufficient**: if this module succeeds, then no subsequent modules in the stack are executed; if it fails, then it doesn't necessarily cause the stack to fail, unless it is the only one in the stack
    * **include**: see man page for pam.d
    * **substack**: see man page for pam.d
* **MODULE-PATH** gives the file name of the library to be found in `/lib*/security` in either absolute or relative path form
* **MODULE-ARGUMENTS** can be given to modify the PAM module's behavior
  
### 33.8: LDAP Authentication
----
* **LDAP** (**L**ightweight **D**irectory **A**ccess **P**rotocol) is a protocol for using and administering distributed directory services over the network; meant to be open and vendor-neutral
* when used for centralized authentication, each client connects to a centralized LDAP server, preferable using TLS (Transport Layer Security)
* LDAP uses PAM and **system-config-authentication** or **authconfig-tui**
    * one has to specify the server, search base domain name, and TLS
    * **openldap-clients**, **pam ldap**, and **nss-pam-ldapd** are also required
* five files are changed when configuring a system for authentication:
    * `/etc/openldap/ldap.conf`
    * `/etc/pam_ldap.conf/`
    * `/etc/nslcd.conf`
    * `/etc/sssd/sssd.conf`
    * `/etc/nsswitch.conf`
* these can be edited manually or using a utilitiy such as **system-config-authentication** or **authconfig-tui**
  
### Lab 33.1: PAM Configuration
----
1. Edit `/etc/pam.d/sshd` and configure it to deny login after three failed attempts. Hint: add the following two lines to `/etc/pam.d/sshd`:
    * `auth required pam_tally2.so deny=3 onerr=fail`
    * `account required pam_tally2.so`
    * done
2. Try to login three times as a particular user (who has an account) while mistyping the password.
    * done
3. Try to login as the same user with the correct password.
    * still "Permission denied" as expected
4. Check to see how many failed logins there are for the user.
    * `$ sudo pam_tally2 -u rocky`
5. Reset the failed login counter.
    * `$ sudo pam_tally2 -u rocky -r`
6. Check again to see how many failed logins there are.
    ```
    Login           Failures Latest failure     From
    rocky               0 
    ```
7. Try to login again with the correct password.
    * works fine
  
### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
accounts, auth | `/etc/pam.d` | directory containing files of authentication rules for each application or service using PAM | LFS201 33.5
accounts, auth | `/lib*/security` | contains modules used by PAM for authentication | LFS201 33.7
accounts, auth | `/etc/openldap/ldap.conf` | file that needs editing when configuring a system for LDAP | LFS201 33.8
accounts, auth | `/etc/pam_ldap.conf/` | file that needs editing when configuring a system for LDAP | LFS201 33.8
accounts, auth | `/etc/nslcd.conf` | file that needs editing when configuring a system for LDAP | LFS201 33.8
accounts, auth | `/etc/sssd/sssd.conf` | file that needs editing when configuring a system for LDAP | LFS201 33.8
accounts, auth | `/etc/nsswitch.conf` | file that needs editing when configuring a system for LDAP | LFS201 33.8
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
accounts, auth | **libpam** | checks for files in `/etc/pam.d` after being called by an app using PAM | LFS201 33.5
accounts, auth | **system-auth** | involved in authorization | LFS201 33.5
accounts, auth | **LDAP** | enables distributed directory services | LFS201 33.8
accounts, auth | **system-config-authentication** | utility involved in PAM and/or LDAP | LFS201 33.8
accounts, auth | **authconfig-tui** | utility involved in PAM and/or LDAP | LFS201 33.8
accounts, auth | **openldap-clients** | involved in configuring LDAP with PAM | LFS201 33.8
accounts, auth | **pam ldap** | involved in configuring LDAP with PAM | LFS201 33.8
accounts, auth | **nss-pam-ldapd** | involved in configuring LDAP with PAM | LFS201 33.8
accounts, auth | `$ sudo pam_tally2 -u rocky` | see how many failed logins there are for user rocky | LFS201 Lab 33.1
accounts, auth | `$ sudo pam_tally2 -u rocky -r` | reset the failed login counter for user rocky | LFS201 Lab 33.1
  