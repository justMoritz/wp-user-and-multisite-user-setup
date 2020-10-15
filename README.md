# Change WP Users for all Accounts on Production Server (Liquidweb)

#### 1) Make updates to the script file
Open `m_wp_user_setup` and change the passwords you want, add the users you want (the pair of lines of code under # adding new users / change user passwords)

#### 2) Copy/Edit the file to the server 
ssh into root (yes root) and copy the `m_wp_user_setup` file into `/usr/local/bin/`
The file is probably already going to be there, you might chose just to edit it.

#### 3) Execute for all users
cd into the home directory on the server with `cd /home` and run the following command:

```for d in ./*/ ; do (set -x && cd "$d" && . /usr/local/bin/m_wp_user_setup); done```

It will loop through all directories and execute the shell script you copied, which in turn will set up the users contained in that file.

### Notes:
* To globally change the passwords for a user, you can also use this file
* You can also delete users with this file, see the sample file for documentation


---


# Update all mmcplatform sites

While the script above does create users for mmcplaform, it does not create them for each and every site *on* mmcplatform. To add all users to all mmcplatform sites:

### Instructions
ssh into mmcplatform (no need for root) and execute the following command

```. /usr/local/bin/m_wp_multisite_user_setup```

That's it! See the file for documentation on how it works!

---

## Fun fact

Before I broke it out into its own script I tried to write a single command that would be able to add all the users. I was able to do it, but it's an illegible mess:
```for i in $(echo $(wp-cli-php /usr/bin/wp-cli.phar  site list --field=url --format=csv) | sed "s/,/ /g");  do (  for j in $(echo $(wp-cli-php /usr/bin/wp-cli.phar  user list --field=user_login --format=csv) | sed "s/,/ /g");  do ( wp-cli-php /usr/bin/wp-cli.phar  user set-role "$j" administrator --url="$i" ); done; );  done```

