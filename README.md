# openldap-server-on-docker
This is good for quick tests and will use: https://github.com/osixia/docker-openldap

1. Create an EC2 instance (Ubunutu) on AWS with the right networking (i.e. your security group should allow incoming tcp traffic on the ldap port 389)
2. SSH into the EC2 instance
3. Install Docker: `curl https://releases.rancher.com/install-docker/28.1.sh | sh`
4. Run: `docker run -p 389:389 -p 636:636 --name my-openldap-container --detach osixia/openldap:1.5.0`
5. Test it by running: `docker exec my-openldap-container ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin`
6. It should be running locally too, test with: `nc -vz localhost 389`
7. Install ldap-utils: `apt install ldap-utils`
8. Now you should be able to run the following locally: `ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin`
9. Use the `users.ldif` file from this repo to create a users OU with some users by running: `ldapadd -h localhost -p 389 -D "cn=admin,dc=example,dc=org" -w admin -c  < users.ldif`
10. Set the password for users `kourosh` with: `ldappasswd -H ldap://localhost -D "cn=admin,dc=example,dc=org" -w admin -S "uid=kourosh,ou=users,dc=example,dc=org" -s password`
11. See the file named `Rancher-OpenLDAP-Config.png` in this repo for how to configure Rancher to use OpenLDAP for Authentication.
