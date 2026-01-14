# run an OpenLDAP server in a docker container and configure Rancher to use OpenLDAP authentication
## Configuration for quick tests that uses: https://github.com/osixia/docker-openldap

1. Create an EC2 instance (Ubunutu) on AWS with the right networking (i.e. your security group should allow incoming TCP traffic on the ldap port 389 and 686 if you want to use SSL)
2. SSH into the EC2 instance
3. Install Docker: `curl https://releases.rancher.com/install-docker/28.1.sh | sh`
4. Run: `docker run -p 389:389 -p 636:636 --name my-openldap-container --detach osixia/openldap:1.5.0`
5. Test it by running: `docker exec my-openldap-container ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin`
6. It should be running locally too, test with: `nc -vz localhost 389`
7. Install ldap-utils: `sudo apt install ldap-utils`
8. Now you should be able to run the following locally: `ldapsearch -x -H ldap://localhost -b dc=example,dc=org -D "cn=admin,dc=example,dc=org" -w admin`
9. Use the `users.ldif` file from this repo to create a users OU with some users by running: `ldapadd -H ldap://localhost:389 -D "cn=admin,dc=example,dc=org" -w admin -c  < users.ldif`
10. Set the password for any of the users, i.e. for user `kourosh` use: `ldappasswd -H ldap://localhost -D "cn=admin,dc=example,dc=org" -w admin -S "uid=kourosh,ou=users,dc=example,dc=org" -s password`
11. See the file named `Rancher-OpenLDAP-Config.png` in this repo for how to configure Rancher to use OpenLDAP for Authentication.

<br/>

## Now, we can also run a php LDAP admin UI using: https://github.com/osixia/docker-phpLDAPadmin

1. The website will run on port 6443 so make sure to enable inbound TCP traffic on that port in your AWS security group
2. Run:
```
docker run -p 6443:443 \
        --env PHPLDAPADMIN_LDAP_HOSTS=ldap.example.com \
        --detach osixia/phpldapadmin:0.9.0
```
Make sure you replace ldap.example.com with the IP of the server

3. Now simply access interface using `https://<LDAP_SERVER_IP>:6443`
4. When you login, make sure to use `cn=admin,dc=example,dc=org` as the username with the password `admin`

<br/>

## To-Do (in progress):
Make this K8s friendly so it can be easily deployed with a GitOps approach. (fleet directory)
