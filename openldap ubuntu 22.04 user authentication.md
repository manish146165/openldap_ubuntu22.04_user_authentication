  

Setting up OpenLDAP & SSSD w/Sudo on Ubuntu 22.04

  
  

[Step1: Set Hostname 2](https://docs.google.com/document/d/1zFz9DdB4sbeNjFOzFhRzNhhhJxXT2LxQZN5jwnMnSZM/edit?tab=t.0#heading=h.vtn14nkduz8)

[Step2: Install OpenLDAP 3](https://docs.google.com/document/d/1zFz9DdB4sbeNjFOzFhRzNhhhJxXT2LxQZN5jwnMnSZM/edit?tab=t.0#heading=h.khz8bgped708)

[Step3: Configure OpenLDAP 4](https://docs.google.com/document/d/1zFz9DdB4sbeNjFOzFhRzNhhhJxXT2LxQZN5jwnMnSZM/edit?tab=t.0#heading=h.c4ib0cxyiqal)

[Step4: Setup LDAPS with SSL/TLS 8](https://docs.google.com/document/d/1zFz9DdB4sbeNjFOzFhRzNhhhJxXT2LxQZN5jwnMnSZM/edit?tab=t.0#heading=h.gtwkmyfwiqqc)

[Step5: Add Certs to OpenLDAP Config 12](https://docs.google.com/document/d/1zFz9DdB4sbeNjFOzFhRzNhhhJxXT2LxQZN5jwnMnSZM/edit?tab=t.0#heading=h.t8zu865ag5hi)

[Step6: Create OpenLDAP Structure 14](https://docs.google.com/document/d/1zFz9DdB4sbeNjFOzFhRzNhhhJxXT2LxQZN5jwnMnSZM/edit?tab=t.0#heading=h.d76hr83hpesz)

[Step7: Setup LDAP Sudo Access 15](https://docs.google.com/document/d/1zFz9DdB4sbeNjFOzFhRzNhhhJxXT2LxQZN5jwnMnSZM/edit?tab=t.0#heading=h.94ce5qr48mrj)

[Step8: Create OpenLDAP Test User 18](https://docs.google.com/document/d/1zFz9DdB4sbeNjFOzFhRzNhhhJxXT2LxQZN5jwnMnSZM/edit?tab=t.0#heading=h.hl3ub99n3xuv)

[Step9: Install SSSD 20](https://docs.google.com/document/d/1zFz9DdB4sbeNjFOzFhRzNhhhJxXT2LxQZN5jwnMnSZM/edit?tab=t.0#heading=h.ukkvabfhqmf1)

  
  
  
  
  
  
  

The objective of this task is to configure an Ubuntu client system to authenticate users from a central LDAP server. This involves setting up the necessary configurations, testing connectivity with the LDAP server, and ensuring that LDAP users can log in to the client system.

  
  
  
  
  
  

# Step1: Set Hostname

1.1: We'll first assign the Fully Qualified Domain Name (FQDN) as the hostname of the server

  

| $ sudo hostnamectl set-hostname ldap.domain.com |
| --- |

  

1.2: Then we'll edit our hosts file to include the FQDN

  

| $ sudo nano /etc/hosts |
| --- |

  

1.3: Add the server's FQDN to the localhost entry 127.0.1.1 entry. We're adding it to a localhost address instead of the server's assigned IP so we can restrict non-encrypted LDAP connections.

  

| 127.0.0.1 localhost127.0.1.1 ldap ldap.domain.com |
| --- |

  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  

# Step2: Install OpenLDAP

2.1: Installing OpenLDAP is pretty straightforward. The one thing to remember is that Slapd is the name of the OpenLDAP service.

  

| $  sudo apt install slapd ldap-utils |
| --- |

  

While we need to create and confirm a new OpenLDAP Admin Password, don't sweat remembering it as we'll be replacing it immediately in the next step.

\[Figure 2.1\]

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfWeOUQ5Jtwe_It-OFe2Hqdum5RX41DbFffWkFnujZ-9nC60JOAuI8Tx6qi_auKKQQCHu4tfu56zR7azdIte8zKm5pTPH3abDIDpeZ03ZBZwsXWigrOerjo4gLN987Zs_6hOXO-jg?key=YWKXUFAXOTwWm4Vr4RvusQre)

  
  
  
  

# Step3: Configure OpenLDAP

3.1: Run the Slapd configurator which includes resetting the OpenLDAP password in the previous setp. Remember to substitute your environment values for the domain and organization names.

  

| $  sudo dpkg-reconfigure slapd |
| --- |

  

| Slapd ConfigurationOmit OpenLDAP server configuration: NoDNS domain name: domain.comOrganization name: Org nameAdministrator Password: New password (Can reuse same password from previous step)Do you want the database to be removed when slapd is purged: NoMove old database: Yes |
| --- |

\[Figure 3.1\]

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcKu2evBLf9rFHzz5_26lKwiG4rKjhrsv4Wl61OKb-UwdEp4Pr-56013pdxZDGXD0Qj7GBiCwnjKxrT8ElkjWxH2cgyrMTTjlaDkBX4Bjvfh0ysgr6a5jKDCvjygQ_Do9pvrFT1AQ?key=YWKXUFAXOTwWm4Vr4RvusQre)

  

\[Figure 3.2\]

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXcfVM-5g83dFCvUrC_oBraULbJh829Xh7efbXlL3U5Qxekozudy7AcsyGqObHab8ar_AOigQy1q4WFH1hr8qzuTKs8t_prH7O1e2C9icPo5YO81L-WepnMJCw3gd2jdHL_ISB_PAQ?key=YWKXUFAXOTwWm4Vr4RvusQre)

\[Figure 3.3\]

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfWeOUQ5Jtwe_It-OFe2Hqdum5RX41DbFffWkFnujZ-9nC60JOAuI8Tx6qi_auKKQQCHu4tfu56zR7azdIte8zKm5pTPH3abDIDpeZ03ZBZwsXWigrOerjo4gLN987Zs_6hOXO-jg?key=YWKXUFAXOTwWm4Vr4RvusQre)

  
  

\[Figure 3.4\]

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfzvREwYlrsJhvvm9gC7dsRoG64N7I0hQdey3GZ9IT34QXQE9LTbkrb-4hOBupM9ddWG9obwW4ibKfdvl01hxMhp0p-wycsiwgfncvA6VgK2jXvi6JJ7GA5_JTZweg_A4S88awlPA?key=YWKXUFAXOTwWm4Vr4RvusQre)

\[Figure 3.5\]

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXf7Gvds5J6Dy6vvaACAmewm0yBsl_-rzX6C6MxuKPfjpQJ40Yadfu_v-p5OSBrLGYb1qBpKrlL4iOxjePuBEO9oGd74_gx7QS16k4DTL5AiSrmK3B4LGjmPMRQaCmY9cjksMhd3LQ?key=YWKXUFAXOTwWm4Vr4RvusQre)

  
  

\[Figure 3.5\]

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdXdJMqNRYWBK79xkoKbNO7FxvAESkA-zsdwl4vmbs3N9HzFtTCo7EfN5KuLZngNL64pEnNNpxHkLT3DmLmzNK3FabN4jt7paL2n88oENwxPl8sAIyL9FVfoyoD2Rcn9eOvib3i_w?key=YWKXUFAXOTwWm4Vr4RvusQre)

3.2: Next we'll edit the ldap.conf file

  

| $  sudo nano /etc/ldap/ldap.conf |
| --- |

  

3.3: Add the following lines below the commented BASE & URI lines

  

| #BASE   dc=example,dc=com#URI    ldap://ldap.example.com ldap://ldap-provider.example.com:666BASE    dc=domain,dc=comURI     ldap://ldap.domain.com |
| --- |

  

| *NOTE: For the rest of this guide, whenever you see dc=domain,dc=com make sure to change it match the domain name in your environment, i.e. tester.org would be dc=tester,dc=org |
| --- |

  

3.4: Now we'll test if OpenLDAP is working correctly

  

| $ ldapsearch -x |
| --- |

  

You should see result: 0 Success if OpenLDAP is working. The 0 means there is no errors.

# Step4: Setup LDAPS with SSL/TLS

Next we're going to secure OpenLDAP by implementing the LDAPS protocol which uses SSL/TLS to encrypt any traffic between the server and its clients. While technically this is not needed for OpenLDAP to work, it is highly encouraged.

  

4.1: Start by installing the following SSL packages

| $ sudo apt install gnutls-bin ssl-cert |
| --- |

  

4.2: Then generate the OpenLDAP Certificate Authority (CA) key

  

| $ sudo certtool --generate-privkey --bits 4096 --outfile /etc/ssl/private/ldap_ca_key.pem |
| --- |

  
  
  

4.3: Now we'll create a template for the OpenLDAP CA

  

| $ sudo nano /etc/ssl/ldap_ca.info |
| --- |

  

4.4: Change the Org Name and set the expiration\_days to how long you'd like the CA cert to be valid

  

| cn = Org Namecacert_signing_keyexpiration_days = 3650 |
| --- |

  

4.4: With the template created, we'll now generate the OpenLDAP CA self-signed cert

  

| sudo certtool --generate-self-signed \--load-privkey /etc/ssl/private/ldap_ca_key.pem \--template /etc/ssl/ldap_ca.info \--outfile /usr/local/share/ca-certificates/ldap_ca_cert.crt |
| --- |

  
  
  
  
  
  

4.5: The following command will add your cert to the server's Trusted CA as well as create a symlink to the cert in /etc/ssl/certs

  

| $ sudo update-ca-certificates |
| --- |

  

4.6: We now need to generate a private key which will be used to decrypt communications with the OpenLDAP clients.

  

| $ sudo certtool --generate-privkey --bits 2048 \--outfile /etc/ldap/sasl2/ldap_slapd_key.pem |
| --- |

  

4.7: Like with the CA, we'll create a template for our OpenLDAP cert

  

| $ sudo nano /etc/ssl/ldap.info |
| --- |

  

4.8: Change the Org Name and expiration\_days. This should be less than or equal to the CA cert's expiration\_days. In addition, you need to set the cn to the FQDN of the OpenLDAP server.

| organization = Org Namecn = ldap.domain.comtls_www_serverencryption_keysigning_keyexpiration_days = 3650 |
| --- |

4.9: With the template created, we'll generate the OpenLDAP self-signed cert which will be copied to any device that authenticates to our OpenLDAP server.

  

| sudo certtool --generate-certificate \--load-privkey /etc/ldap/sasl2/ldap_slapd_key.pem \--load-ca-certificate /etc/ssl/certs/ldap_ca_cert.pem \--load-ca-privkey /etc/ssl/private/ldap_ca_key.pem \--template /etc/ssl/ldap.info \--outfile /etc/ldap/sasl2/ldap_slapd_cert.pem |
| --- |

  

4.10: Now change permissions on the newly generated OpenLDAP cert

  

| sudo chown openldap. -R /etc/ldap/sasl2sudo chmod 0640 /etc/ldap/sasl2/ldap_slapd_key.pem |
| --- |

  
  

4.11: Lastly, we'll verify our OpenLDAP cert against our CA cert

  
  

| openssl verify -CAfile /etc/ssl/certs/ldap_ca_cert.pem /etc/ldap/sasl2/ldap_slapd_cert.pem |
| --- |

  
  
  
  
  

# Step5: Add Certs to OpenLDAP Config

5.1: With our certificates generated, we'll need to add them to our OpenLDAP schema. We need to first create an LDIF file with our intended changes.

  

| sudo nano /etc/ldap/schema/cert.ldif |
| --- |

  

| *NOTE: All OpenLDAP changes are made via .ldif text files. You need to create these files first and then apply them to your existing schema. While a bit of a hassle, it does make reusing the same functions easier. |
| --- |

  
  
  

| dn: cn=configadd: olcTLSCACertificateFileolcTLSCACertificateFile: /etc/ssl/certs/ldap_ca_cert.pem-add: olcTLSCertificateFileolcTLSCertificateFile: /etc/ldap/sasl2/ldap_slapd_cert.pem-add: olcTLSCertificateKeyFileolcTLSCertificateKeyFile: /etc/ldap/sasl2/ldap_slapd_key.pem |
| --- |

  
  
  
  
  

5.2: We'll now apply the ldif file to our schema. If the command works, you'll see modifying entry "cn=config" and no errors.

  

| sudo ldapmodify -Y EXTERNAL -H ldapi:/// -f /etc/ldap/schema/cert.ldif |
| --- |

  

5.3: Now edit the Slapd default parameters

  

| sudo nano /etc/default/slapd |
| --- |

  

5.4: Find the line that starts with SLAPD\_SERVICES and replace it with the following. This will restrict unencrypted LDAP traffic to only localhost and also enable secured LDAPS connections.

  

| SLAPD_SERVICES="ldap://127.0.1.1/ ldapi:/// ldaps:///" |
| --- |

  

5.5: We'll need to restart slapd, the OpenLDAP service, after making these changes

  

| sudo systemctl restart slapd |
| --- |

  

5.6: Now verify LDAPS is working

  

| ldapsearch -x -H ldaps://ldap.domain.com |
| --- |

  
  
  
  
  
  
  

# Step6: Create OpenLDAP Structure

6.1: With all of our OpenLDAP components in place, we'll be creating a basic structure of OU's for our LDAP schema.

  

| sudo nano /etc/ldap/schema/rootOU.ldif |
| --- |

  

6.2: This ldif will create the Users & Groups OU's at the top of our schema and an administrators group which we'll be granting sudo rights later in the guide.

  

| dn: ou=Users,dc=domain,dc=comobjectClass: organizationalUnitou: Usersdn: ou=Groups,dc=domain,dc=comobjectClass: organizationalUnitou: Groupsdn: cn=administrators,ou=Groups,dc=domain,dc=comobjectClass: posixGroupcn: administratorsgidNumber: 5000 |
| --- |

  
  
  

6.3: With the ldif created, we'll add these entries into our LDAP schema. When prompted, enter the OpenLDAP admin password set earlier in the guide.

  

| ldapadd -x -D cn=admin,dc=domain,dc=com -W -f /etc/ldap/schema/rootOU.ldif |
| --- |

  
  

# Step7: Setup LDAP Sudo Access

 7.1: Setting up LDAP enabled sudo access is not as straightforward as you may expect. The default sudo package Ubuntu uses doesn't include support for LDAP, so we need to replace it with the sudo-ldap version instead.

  

| export SUDO_FORCE_REMOVE=yessudo -E bash -c 'apt install sudo-ldap' |
| --- |

  

7.2: We'll now copy the sudo schema provided by the sudo-ldap package to our ldap schema directory

  

| sudo cp /usr/share/doc/sudo-ldap/schema.olcSudo /etc/ldap/schema/ |
| --- |

  
  

7.3: Then add it to our schema

  

| sudo ldapadd -Y EXTERNAL -H ldapi:/// -f /etc/ldap/schema/schema.olcSudo |
| --- |

  
  
  
  
  

7.4: Next we'll create an ldif which defines the Sudoers OU and sets the default sudo options for it in our schema

  

| sudo nano /etc/ldap/schema/sudoers.ldif |
| --- |

  

7.5: This ldif will also grant the LDAP group administrators we created earlier sudo rights.

  
  

| dn: ou=Sudoers,dc=domain,dc=comobjectClass: organizationalUnitobjectClass: topou: Sudoersdn: cn=defaults,ou=Sudoers,dc=domain,dc=comobjectClass: sudoRoleobjectClass: topcn: defaultssudoOption: env_resetsudoOption: mail_badpasssudoOption: secure_path=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bindn: cn=%administrators,ou=Sudoers,dc=domain,dc=comobjectClass: topobjectClass: sudoRolecn: %administratorssudoUser: %administratorssudoHost: ALLsudoCommand: ALL |
| --- |

  
  

7.6: Finally add the sudoers.ldif to the schema and enter the OpenLDAP admin password when prompted

  

| ldapadd -x -D cn=admin,dc=domain,dc=com -W -f /etc/ldap/schema/sudoers.ldif |
| --- |

  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  

# Step8: Create OpenLDAP Test User

 8.1: We'll now create an LDAP test user and grant it sudo rights by adding it        to our administrators group.

  

| sudo nano /etc/ldap/schema/addUser.ldif |
| --- |

  
  

| dn: uid=jnobody,ou=Users,dc=domain,dc=comobjectClass: inetOrgPersonobjectClass: posixAccountobjectClass: shadowAccountuid: johnsn: NobodygivenName: Johncn: John NobodydisplayName: John NobodyuidNumber: 10000gidNumber: 5000userPassword: {CRYPT}xgecos: John NobodyloginShell: /bin/bashhomeDirectory: /home/jnobody |
| --- |

  

8.2: Add the addUser.ldif to the schema and enter the OpenLDAP admin password

  

| ldapadd -x -D cn=admin,dc=domain,dc=com -W -f /etc/ldap/schema/addUser.ldif |
| --- |

  

8.3: Next we'll set an LDAP password for our test user.

  

| ldappasswd -x -D cn=admin,dc=domain,dc=com -W -S uid=jnobody,ou=Users,dc=domain,dc=com |
| --- |

  

Enter and confirm the test user password and then enter the OpenLDAP admin password

  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  
  

# Step9: Install SSSD

 Now we're going to setup SSSD which is a service that authenticates to OpenLDAP. It also has the added ability of caching credentials in the event an OpenLDAP server become unreachable.

  

9.1: First install SSSD and the accompanying packages

  
  

| sudo apt install sssd-ldap ldap-utils libsss-sudo |
| --- |

  
  
  

9.2: We'll now create the sssd.conf and change the permissions or else SSSD won't start

  
  

| sudo install -m 600 /dev/null /etc/sssd/sssd.conf |
| --- |

  

9.3: Edit the sssd.conf and add the example config below

  

| sudo nano /etc/sssd/sssd.conf |
| --- |

  
  
  
  
  
  
  
  
  
  
  

9.4: You'll want to change lines 3, 5, 8 & 10 to match the domain name used in your environment

  

| [sssd]config_file_version = 2domains = domain.com[domain/domain.com]id_provider = ldapauth_provider = ldapldap_uri = ldaps://ldap.domain.comcache_credentials = Trueldap_search_base = dc=domain,dc=com |
| --- |

  

9.5: With our SSSD config setup, we'll we'll want to restart the SSSD service

  

| sudo systemctl restart sssd |
| --- |

  
  

9.6: The following allows the automatic creation of a home directory for an LDAP user that logs into the OpenLDAP server

  
  

| sudo pam-auth-update --enable mkhomedir |
| --- |

  
  

9.7: You can now switch to this LDAP user and test sudo rights

  

| su jnobodysudo ls -la |
| --- |

  

\[Figure9.7-1\]

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXeOYCVR_GWy8KnoOgbtDEHComu6UmaDw1v7c7CuTNZP2dnC6G38ZOeLKgY1GpgeBUqtsPYcSByD07ULB8Hg7MoqS29SEFGocoo3nqhT3JJdwiOzzFyv4iCpctgWu3Hn6NQfP335aw?key=YWKXUFAXOTwWm4Vr4RvusQre)

  

\[Figure9.7-2\]

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXfLO7xM_v3O5421qMMb2bNm0Foelc6PVChmnu-D1CgQzH8cokqA0_F6roJcRELWxhiZCpNJUxn0sRT5E4YJT0XBrHE1TEJpB5hsN5x0qCR-Nd9cGKuMXCHFbEYoPXGLSn5ckCiLsA?key=YWKXUFAXOTwWm4Vr4RvusQre)

\[Figure9.7-3\]

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXc8sZfwZmkWGi6GF5nqpUy5YBK4gDhOgtoaKvanwJVQGQG0QmvQWLI6Jtpx-f9Z43KdiQN4U8LWGa-Mq7tWP1yi8syfkzc6uyWzIRbmaqMZN_cX7ufbHAz8Pqc_BXCT5HMXO-ov7Q?key=YWKXUFAXOTwWm4Vr4RvusQre)

  

\[Figure9.7-4\]

![](https://lh7-rt.googleusercontent.com/docsz/AD_4nXegBkaKmDeCsvn-mYTto3-AM6przml135raFwwYq8cm7kq9Rz1h9eFiHxhPZ8SJqOnOFh0i7iQq3qgqqBM0MutRaHN4SI2ssO7hbUyXh73OkNk6Uq4vcPKJ2qfAsQQ6oHvq9hkn5Q?key=YWKXUFAXOTwWm4Vr4RvusQre)