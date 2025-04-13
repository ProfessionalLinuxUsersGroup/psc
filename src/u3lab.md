<div class="flex-container">
        <img src="https://github.com/ProfessionalLinuxUsersGroup/img/blob/main/Assets/Logos/ProLUG_Round_Transparent_LOGO.png?raw=true" width="64" height="64"></img>
    <p>
        <h1>Under Construction</h1>
    </p>
</div>
Unit 3 Lab – Identity and Access Management 

Required Materials 

Putty or other connection tool 
Lab Server 
Root or sudo command access 
STIG Viewer 2.18 (download from https://public.cyber.mil/stigs/downloads/ ) 


EXERCISES (Warmup to quickly run through your system and familiarize yourself) 

1.
```
ls -l /etc/pam.d/ 
```
	 a. What are the permissions and names of files? Can everyone read them? 
	
```
cat /etc/pam.d/sshd 
```
	 a. What information do you see in this file? b. Does any of it look familiar to you? 
 

 ##PreLAB 
 
Download the STIG Viewer 2.18 from - https://public.cyber.mil/stigs/downloads/ 
(Insert picture here Chris)

Download the STIG for RHEL 9 and the import it into your STIG viewer 
(Insert picture here Chris)

Create a checklist from the opened STIG for RHEL 9 
(Insert picture here Chris)

## LAB 
 
This lab is designed to have the engineer practice securing a Linux server or service against a set of configuration standards. These standards are sometimes called benchmarks, checklists, or guidelines. The engineer will be using STIG Viewer 2.18 to complete this lab. 

## PAM configuration: 

1. Connect to a hammer server 
2. Filter by pam and see how many STIGS you have. (Why is it really only 16?) 
(Insert picture here Chris)

3 Examine STIG V-257986 
	 a. What is the problem? 
	 b. What is the fix? 
	 c. What type of control is being implemented? 
	 d. Is it set properly on your system? 
		 i. grep -i pam /etc/ssh/sshd_config 
		 ii. Can you remediate this finding? 

4 Check and remediate STIG V-258055 
	 a. What is the problem? 
	 b. What is the fix? 
	 c. What type of control is being implemented? 
		 i. Are there any major implications to think about with this change on your system? Why or why not? 
1. d. Is it set properly on your system? 
2. e. How would you go about remediating this on your system? 

5 Check and remediate STIG V-258098 
	a. What is the problem? 
 	b. What is the fix? 
 	c. What type of control is being implemented? 
 	d. Is it set properly on your system? 

6 Filter by “password complexity” 
 (Insert picture Chris)
	 a. How many are there? 
	 b. What are the password complexity rules? 
		i. Are there any you haven’t seen before? 
	
7 Filter by sssd 
	 a. How many STIGS do you see? 
	 b. What do these STIGS appear to be trying to do? What types of controls are they? 

### OpenLDAP Setup 

You will likely not build an LDAP server in a real world environment. We are doing it for understanding and ability to complete the lab. In a normal corporate environment this is likely Active Directory. 

*To simplify some of the typing in this lab, there is a file located at /lab_work/identity_and_access_management.tar.gz that you can pull down to your system with the correct .ldif files.
```
cp /lab_work/identity_and_access_management.tar.gz .
tar -xzvf identity_and_access_management.tar.gz
```

 ## Install and configure OpenLDAP 
 
a. Stop the warewulf client 
```
systemctl stop wwclient 
```
b. Edit your /etc/hosts file 
	 *Look for and edit the line that has your current server*
```
vi /etc/hosts
192.168.200.175 hammer25 hammer25-default ldap.prolug.lan ldap 
```
c. Setup dnf repo 
```
[root@hammer25 ~]# dnf config-manager --set-enabled plus 
[root@hammer25 ~]# dnf repolist 
[root@hammer25 ~]# dnf -y install openldap-servers openldap-clients openldap 
```
d. Start slapd
```
[root@hammer25 ~]# systemctl start slapd 
[root@hammer25 ~]# ss -ntulp
```
e. Allow ldap through the firewall 
```
firewall-cmd --add-service={ldap,ldaps} --permanent 
firewall-cmd --reload 
firewall-cmd --list-all
``` 
f. Generate a password (Our example uses *testpassword*) This will return a salted SSHA password. *Save this password and salted hash for later*

```
[root@hammer25 ~]# slappasswd 
New password: 
Re-enter new password: 
{SSHA}wpRvODvIC/EPYf2GqHUlQMDdsFIW5yig 
```
	
g. Change the password (Now we will edit the config and enter the previous password hash we generated)
```
	 [root@hammer25 ~]# vi changerootpass.ldif 
		 
		 dn: olcDatabase={0}config,cn=config 
		 changetype: modify 
		 replace: olcRootPW 
		 olcRootPW: {SSHA}vKobSZO1HDGxp2OElzli/xfAzY4jSDMZ 
	
```
*Now we add it*	
```
[root@hammer25 ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f changerootpass.ldif 
SASL/EXTERNAL authentication started SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth 
SASL SSF: 0 
modifying entry "olcDatabase={0}config,cn=config" 
```

h. Generate basic schemas 
	```
	[root@hammer25 ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/cosine.ldif 
	[root@hammer25 ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/nis.ldif 
	[root@hammer25 ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/openldap/schema/inetorgperson.ldif 
	```
	
i. Set up the domain (#USE THE PASSWORD YOU GENERATED EARLIER)
	
```
	 [root@hammer25 ~]# vi setdomain.ldif 
	 
	 dn: olcDatabase={1}monitor,cn=config 
	 changetype: modify 
	 replace: olcAccess 
	 olcAccess: {0}to * by 
	 dn.base="gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth" read by dn.base="cn=Manager,dc=prolug,dc=lan" read by * none 
	 dn: olcDatabase={2}mdb,cn=config 
	 changetype: modify 
	 replace: olcSuffix 
	 olcSuffix: dc=prolug,dc=lan 
	 
	 dn: olcDatabase={2}mdb,cn=config 
	 changetype: modify 
	 replace: olcRootDN 
	 olcRootDN: cn=Manager,dc=prolug,dc=lan dn: olcDatabase={2}mdb,cn=config changetype: modify 
	 add: olcRootPW 
	 olcRootPW: {SSHA}Uf13AbVHOcs/aDWJOvDxxfBSl3omExG2 
	 
	 dn: olcDatabase={2}mdb,cn=config 
	 changetype: modify 
	 add: olcAccess 
	 olcAccess: {0}to attrs=userPassword,shadowLastChange by dn="cn=Manager,dc=prolug,dc=lan" write by anonymous auth by self write by * none 
	 olcAccess: {1}to dn.base="" by * read 
	 olcAccess: {2}to * by dn="cn=Manager,dc=prolug,dc=lan" write by * read 
```


## Run it 
```

	[root@hammer25 ~]# ldapmodify -Y EXTERNAL -H ldapi:/// -f setdomain.ldif 
	SASL/EXTERNAL authentication started 
	SASL username: gidNumber=0+uidNumber=0,cn=peercred,cn=external,cn=auth SASL SSF: 0 
	modifying entry "olcDatabase={1}monitor,cn=config" 
	modifying entry "olcDatabase={2}mdb,cn=config" 
	modifying entry "olcDatabase={2}mdb,cn=config" 
	modifying entry "olcDatabase={2}mdb,cn=config" 
	modifying entry "olcDatabase={2}mdb,cn=config" 
```
	 
j. Search and verify the domain is working. 
```
[root@hammer25 ~]# ldapsearch -H ldap:// -x -s base -b "" -LLL "namingContexts" 
	dn: 
	namingContexts: dc=prolug,dc=lan 

```
	 
k. Add the base group and organization. 
```
	 vi addou.ldif 
	 
	 dn: dc=prolug,dc=lan 
	 objectClass: top 
	 objectClass: dcObject 
	 objectclass: organization 
	 o: My prolug Organisation 
	 dc: prolug 
	 
	 dn: cn=Manager,dc=prolug,dc=lan 
	 objectClass: organizationalRole 
	 cn: Manager 
	 description: OpenLDAP Manager 
	 
	 dn: ou=People,dc=prolug,dc=lan 
	 objectClass: organizationalUnit 
	 ou: People 
	 
	 dn: ou=Group,dc=prolug,dc=lan 
	 objectClass: organizationalUnit 
	 ou: Group 
	 
	[root@hammer25 ~]# ldapadd -x -D cn=Manager,dc=prolug,dc=lan -W -f addou.ldif 
```

l. Verifying 
	
```
	 [root@hammer25 ~]# ldapsearch -H ldap:// -x -s base -b "" -LLL "+" 
	 [root@hammer25 ~]# ldapsearch -x -b "dc=prolug,dc=lan" ou 
```

m. Add a user 
	Generate a password 
	slappasswd  (#use testuser1234)
	
```
	 [root@hammer25 ~]# vi adduser.ldif 
	 
	 dn: uid=testuser,ou=People,dc=prolug,dc=lan 
	 objectClass: inetOrgPerson 
	 objectClass: posixAccount 
	 objectClass: shadowAccount 
	 cn: testuser 
	 sn: temp 
	 userPassword: {SSHA}dk/Lks9078gfZQJ31ABvPpvKv3sHhr29 
	 loginShell: /bin/bash 
	 uidNumber: 15000 
	 gidNumber: 15000 
	 homeDirectory: /home/testuser 
	 shadowLastChange: 0 
	 shadowMax: 0 
	 shadowWarning: 0 
	 
	 dn: cn=testuser,ou=Group,dc=prolug,dc=lan 
	 objectClass: posixGroup 
	 cn: testuser 
	 gidNumber: 15000 
	 memberUid: testuser 
	 
	 [root@hammer25 ~]# ldapadd -x -D cn=Manager,dc=prolug,dc=lan -W -f adduser.ldif 
``` 

	n. Verify that your user is in the system. 
	```
	[root@hammer25 ~]# ldapsearch -x -b "ou=People,dc=prolug,dc=lan" 
	```
	
o. Secure the system with TLS  (accept all defaults) 

	[root@hammer25 ~]# openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout /etc/pki/tls/ldapserver.key -out /etc/pki/tls/ldapserver.crt 
	
	[root@hammer25 ~]# chown ldap:ldap /etc/pki/tls/{ldapserver.crt,ldapserver.key} 
	
	[root@hammer25 tls]# ls -l /etc/pki/tls/ldap* 
	-rw-r--r--. 1 ldap ldap 1224 Apr 12 18:23 /etc/pki/tls/ldapserver.crt 
	-rw-------. 1 ldap ldap 1704 Apr 12 18:22 /etc/pki/tls/ldapserver.key 
	
	[root@hammer25 ~]#vi tls.ldif 
	dn: cn=config 
	changetype: modify 
	add: olcTLSCACertificateFile 
	olcTLSCACertificteFile: /etc/pki/tls/ldapserver.crt 
	
	add: olcTLSCertificateKeyFile 
	olcTLSCertificateKeyFile: /etc/pki/tls/ldapserver.key 
	
	add: olcTLSCertificateFile 
	olcTLSCertificateFile: /etc/pki/tls/ldapserver.crt 
	
	[root@hammer25 ~]# ldapadd -Y EXTERNAL -H ldapi:/// -f tls.ldif
	
p. Fix the /etc/openldap/ldap.conf to allow for certs 
	 ```
```
[root@hammer25 ~]# vi /etc/openldap/ldap.conf 


# 
# LDAP Defaults 
# 
# See ldap.conf(5) for details 
# This file should be world readable but not world writable. 

#BASE dc=example,dc=com 
#URI ldap://ldap.example.com ldap://ldap-master.example.com:666 


#SIZELIMIT 
#TIMELIMIT 
#DEREF 12 15 never 

# When no CA certificates are specified the Shared System Certificates 
# are in use. In order to have these available along with the ones specified 
# by TLS_CACERTDIR one has to include them explicitly: 

TLS_CACERT /etc/pki/tls/ldapserver.crt 
TLS_REQCERT never 

# System-wide Crypto Policies provide up to date cipher suite which should # be used unless one needs a finer grinded selection of ciphers. Hence, the 
# PROFILE=SYSTEM value represents the default behavior which is in place # when no explicit setting is used. (see openssl-ciphers(1) for more info) 
#TLS_CIPHER_SUITE PROFILE=SYSTEM 
# Turning this off breaks GSSAPI used with krb5 when rdns = false 
SASL_NOCANON on 
```

q. systemctl restart slapd 

```
[root@hammer25 ~]# systemctl restart slapd
```
### SSSD Configuration and Realmd join to LDAP 
 
 SSSD can connect a server to a trusted LDAP system and authenticate users for access to local resources. You will likely do this during your career and it is a valuable skill to work with. 
 
  Install sssd, configure, and validate that the user is seen by the system 
 a.
```
[root@hammer25 ~]# dnf install openldap-clients sssd sssd-ldap oddjob-mkhomedir authselect 
```
b.
```
[root@hammer25 ~]# authselect select sssd with-mkhomedir --force 
```
c.
```
[root@hammer25 ~]# systemctl enable --now oddjobd.service 
 ```
 d.
 ```
[root@hammer25 ~]# systemctl status oddjobd.service 
 ```

e.
Uncomment and fix the lines in /etc/openldap/ldap.conf 
 BASE dc=prolug,dc=lan 
 URI ldap://ldap.ldap.lan/ 

 ```
 [root@hammer25 ~]# vi /etc/sssd/sssd.conf 
 
 [domain/default] 
 id_provider = ldap 
 autofs_provider = ldap 
 auth_provider = ldap 
 chpass_provider = ldap 
 ldap_uri = ldap://ldap.prolug.lan/ 
 ldap_search_base = dc=prolug,dc=lan 
 #ldap_id_use_start_tls = True 
 #ldap_tls_cacertdir = /etc/openldap/certs 
 cache_credentials = True 
 #ldap_tls_reqcert = allow 
 
 [sssd] 
 services = nss, pam, autofs 
 domains = default 
 
 [nss] 
 homedir_substring = /home 
```

g.
```
[root@hammer25 ~]# chmod 0600 /etc/sssd/sssd.conf 
```
h.
```
[root@hammer25 ~]# systemctl start sssd 
```
i.
```
[root@hammer25 ~]#systemctl status sssd 
```
j.
validate that the user can be seen 
```
 [root@hammer25 ~]#id testuser 
  uid=15000(testuser) gid=15000 groups=15000
```
