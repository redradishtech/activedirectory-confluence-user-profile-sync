# Active Directory to Confluence User Profile Synchronizer

This is a command-line utility that copies user records (telephone number, jabber ID, department, location) from Active Directory to Atlassian Confluence users' profiles. It has been tested against Confluence 5.2.3.

Note, this script overwrites user profile data with AD data (if set). You should only run it if AD is your source of truth for user profiles, and you don't mind overwriting any Confluence profile customizations users may have made.

## Installation

You will need Ruby (tested on 1.9.3p0, 2.0.0-p195 and 2.0.0p247), eg:

    # aptitude install ruby-1.9.1  # Yes, package 'ruby-1.9.1' packages ruby 1.9.3 on ubuntu

Some Ruby gems are required before the script will work:

    # aptitude install ruby1.9.1-dev  # Required for building nokogiri
    # gem install trollop net-ldap nokogiri parallel

## Sample Usage

    $ ./sync_ad_confluence.rb \
        --ldaphost "example.ds" \
        --binddn="CN=Service Account for LDAP search and Bind,OU=SERVICE ACCOUNTS,OU=ADMIN,DC=example,DC=ds" \
        --bindpassword="s3cret" \
        --basedn="OU=ADMIN,DC=example,dc=ds" \
        --confbaseurl="http://wiki.example.ds" \
        --confuser="_profilesyncer" \
        --confpassword="confs3cret" \
        admin -v
	admin set profile: {:position=>"Admin role account", :phone=>"(415) 123-4567", :fullname=>"Admin User", :description=>"IT", :username=>"admin", :email=>"admin@example.ds", :location=>"Office location field, somewhere in australia, tasmania, tasmania2, zipcode 1, Australia"}

As the script needs to log in to Confluence as a user with admin rights, usually 'confluence-administrators' group membership. You may wish to create a role account for this ('_profilesyncer' in this example).

It is suggested that the above command be stored in a script, with permissions set to 0700:

    #!/bin/bash -eu
    
    ./sync_ad_confluence.rb \
        --ldaphost "example.ds" \
        --binddn="CN=Service Account for LDAP search and Bind,OU=SERVICE ACCOUNTS,OU=ADMIN,DC=example,DC=ds" \
        --bindpassword="s3cret" \
        --basedn="OU=ADMIN,DC=example,dc=ds" \
        --confbaseurl="http://wiki.example.ds" \
        --confuser="_profilesyncer" \
        --confpassword="confs3cret" \
        $*  
    

## How it works

The script iterates over Person records found in LDAP, and looks for these fields:

* samaccountname
* displayname
* mail
* telephonenumber
* jabberid
* description
* department
* company
* physicaldeliveryofficename
* streetaddress
* l
* st
* postalcode
* co

Where:

* samaccountname, displayname and mail map to the Confluence user's username. These fields must be present. All the rest are optional.
* telephonenumber is mapped to the profile 'Phone' field
* jabberid is mapped to the profile 'IM' field
* description is mapped to the profile 'Position' field
* department is mapped to the profile 'Department' field 
* physicaldeliveryofficename, streetaddress, l, st, postalcode and co are joined with commas, and set to the profile 'Location' field

Note that for AD attributes (other than samaccountname, displayname and mail):
* If the AD attribute isn't set, then the Confluence profile field isn't set (or unset) either
* Any existing Confluence profile values are overwritten with the AD value. Users should be warned not to waste their time setting 


## Limitations

Profile pictures are not set, because it is not possible (at least in 5.2.3) for a Confluence administrator to set a user's profile picture. See https://jira.atlassian.com/browse/CONF-13515

