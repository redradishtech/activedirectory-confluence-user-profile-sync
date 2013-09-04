# Active Directory to Confluence User Profile Synchronizer

This is a command-line utility that copies user records (telephone number, jabber ID, department, location) from Active Directory to Atlassian Confluence users' profiles.

## Sample Usage

    $ ./sync_ad_confluence.rb \
        --ldaphost "example.ds" \
        --binddn="CN=Service Account for LDAP search and Bind,OU=SERVICE ACCOUNTS,OU=ADMIN,DC=example,DC=ds" \
        --bindpassword="s3cret" \
        --basedn="OU=ADMIN,DC=example,dc=ds" \
        --confbaseurl="http://wiki.example.ds" \
        --confuser="admin" \
        --confpassword="confs3cret" \
        admin
    Updated http://wiki.example.ds/admin/users/viewuser.action;jsessionid=4501D470FE1ACD4A32638FC3BB4EFD32?username=admin
