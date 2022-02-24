# Easy Amarel

## create and push ssh keys

Follow [these general instructions](https://www.digitalocean.com/community/tutorials/how-to-set-up-ssh-keys-2) for setting up ssh-key-based access to Amarel.

## configure ssh key use

Modify `~/.ssh/config` (you may need to create it if it doesn't exist) on your personal computer by adding:


    Host amarel
        Hostname amarel.rutgers.edu
        User [username]
        IdentityFile ~/.ssh/[keyname]
        ServerAliveInterval 5m
        AddKeysToAgent yes
        UseKeychain yes
        
Replacing `[username]` with your user name and `[keyname]` with the ssh key name that you have created (usually starts with `id_`). 

After doing the above you should be able to log into Amarel using `ssh amarel`. 