# NOTES ON MY JOURNEY TO BE A LINUX ADMIN

## Linux File Permissions

There multiple way to change the permissions on a file, but all of them include using the chmod command. This command let us change the permissions not only on a file but also on a directory.

But before going directly to the options that these command offers us, we should look into how to get the permissions that are applied to the files or directories.

We can get these permissions executing the ls -l command. Doing this, we'll get an output similar to the following:

```
drwxr-xr-x  2 owner group 4096 jun 21  2019 Directory/
-rw-r--r--  1 owner group   32 jun 21  2019 file

```
Here we can observe that we are given at the begining a couple of letters that have a some kind of meaning. The first letter of this string, tell us if it is a Directory (d) or a file (-), the next three letters tell us the owner permissions, then the following three letters indicates us the permissions for all user in the grou, and finally the last three letters the permissions for all other users.

Above, I have mentioned that these permissions are indicated by letters, which indicate the following:

* r > read
* w > write
* x > execute

Now let's get back to our beloved chmod command. This command gives us a lot of flexibility when wanting to change the permissions on a file or directory.

For example we can modify the read, write, and execute permissions by using the previously mentioned letters.

Like:

```
chmod +x filename
```
This will give the file the execute permisssion. But we could also remove this permission just by changing + for a - sign.

Now, let's say that I'm kind of alergic to letters, lucky me, we can do this by using numbers.

For example,

```
chmod 755 file
```
Let's translate this information:

* 1 = execute
* 2 = write
* 4 = read
* 7 = 1 + 2 + 4 means read, write, and execute.
* 5 = 1 + 4 means read and execute. 

But in the above example we have three letters, this is because the first letter, going from left to right, modifies the corresponding permissions for the owner, while second letter for the group, and the last letter the permissions on others.

Anyway, there are lots of differents forms/combinations to modify the permissions on a file or directory, so don't be shy and just google it.


