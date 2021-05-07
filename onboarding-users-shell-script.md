
## Onboarding Users onto Server using Shell Script

### The objective of this project is to create a shell script that can read a .csv file of names of users to be onboarded.

##### Create a directory called 'Shell'
```
mkdir Shell
```

```
cd Shell
```

##### Next, make a file called 'names.csv' and add a random list of names to it
```
vi names.csv
```
```
Sammy
John
Tom
Sarah
Saachi
Nick
Mark
Mary
Angela
Liz
Rachel
Rob
```
##### Create a group called 'developer' 

```
sudo groupadd developer
```

##### Create a Shell Script with name 'onboarding-user.sh with the code:
```sh
#!/bin/bash 

# Automation of user creation from a .csv file
# A file with first names of users is created called 'names.csv'

# Assigning the names file to a variable
USER_FILE=names.csv

# Assigning a variable to the group 'developer'
USER_GROUP=developer

# Assigning a variable to name the .ssh directory of skel
SSH_FILE=/etc/skel/.ssh/

# Assigning a variable to the public key file (authorized_keys)
PUBLIC_KEY_FILE=authorized_keys

# Creating a temporary password for the users and assigning a variable
USER_PASSWORD=password

# check if group developer exists
if [ $(getent group developer) ];
then
    echo The group developer already exists
else
    sudo groupadd $USER_GROUP
    echo The group developer has been successfully created
fi

# Adding a .ssh file to the skel directory of each user if it does not exist
if [ -d "$SSH_FILE" ];
then
    echo "$SSH_FILE already exists"
else
    sudo mkdir -p $SSH_FILE
    sudo touch $SSH_FILE/$PUBLIC_KEY_FILE
    sudo bash -c "cat $PUBLIC_KEY_FILE >> $SSH_FILE/authorized_keys"
fi


# create a user for each name on the file
# To create separate usernames, IFS (Internal Field Separator) is used to identify the spaces
while IFS= read USER_NAME
    do 
        # Verify if the username already exists by checking /etc/passwd file
        # After user creation, add user to group developer
        # Set a password for a duration for 7 days
        # Allow permissions to user for .ssh and authorized_keys file

        if [ $(getent passwd $USER_NAME) ];
        then
            echo $USER_NAME already exists
        else
            sudo useradd -m -G $USER_GROUP  -s /bin/bash $USER_NAME
            sudo echo -e "$USER_PASSWORD\n$USER_PASSWORD" | sudo passwd "${USER_NAME}" 
            sudo passwd -x 7 ${USER_NAME}
            sudo chmod 700 /home/$USER_NAME/.ssh
            sudo chmod 644 /home/$USER_NAME/.ssh/$USER_FILE
            echo "$USER_NAME successfully created"
        fi
    done < $USER_FILE

exit 0
```



