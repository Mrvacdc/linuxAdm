# NOTES ON MY JOURNEY TO BE A LINUX ADMIN

This set of notes is mostly based or guided by the task indicated by the kode kloud platform where you can have simulated real-life hands-on practice as if you were working for a company as a sysadmin/devops.

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

## Linux TimeZones Setting

Let's imagine that you are a very wealthy and you are on holidays and travelling with your computer around the world, you might want to modify the timezone of your system to the timezone of the place you are right now. How we would go about it?

Well, we should first check our current time zone, to do this we run the timedatectl command.

```
$timedatectl
```
We'll get a similar output to the following:

```
               Local time: Wed 2020-05-06 19:33:20 UTC
           Universal time: Wed 2020-05-06 19:33:20 UTC
                 RTC time: Wed 2020-05-06 19:33:22    
                Time zone: UTC (UTC, +0000)           
System clock synchronized: yes                         
              NTP service: active                      
          RTC in local TZ: no  
```

To modify the timezones, we should also know the available time zones that we can set. We can do this by executing timedatectl with the list-timezones option:

```
$timedatectl list-timezones
```
We'll get something like this;

```
...
America/Nipigon
America/Nome
America/Noronha
...
```

Finally, once we have decided ourself on which timezone we want to set, we must run the following command:

```
sudo timedatectl set-timezone your_time_zone
```
For example, we could change the system's timezone to America/Noronha;

```
sudo timedatectl set-timezone America/Noronha
```

We could that our setting has been modified by running the timedatectl command again.

## Create a Linux User with non-interactive shell

What's the meaning of interactive and login when we talk about shells?

By interactive/non interactive shells, we refer to the exchange between the user and the shell. The user types commands into the terminal (input), and the shell prints an output to the screen.

On the other hand, by login/non-login shells, we refer to the event when a user access a system providing its username and password.

At the same time, both interactive and non-interactive can be either login or non-login.

When is an interactive login shell use?

Well, let us say that a group of user that belong to a same area/department need a particular variable set in their sessions, so we when a user that belongs to this group logs into the system, there are some configurations that adjust to its need.

Once a user has logged into a system, and opens other shells, we refer to these as interactive non-login shells.

Apart from these, we also have non-interactive shells. These do not require any kind of human interaction, or we should say that the suer is unable to type commands or it is not asked to do it. And in the case of having some kind of output, this one is written to a log.

A very common case, that probably most of us have once used, are a non-interactive non-login shells. These are automated scripts such as cronjobs that mostly are used for maintenance and repetitive tasks.

Going back to our main goal, of creating a user with non-interactive shell, we should first of all check if the user that we are asked to create does exist.

For this purpose, we simple run the id command:

```
id $username
``` 

After checking this, we can proceed to create our user with a non-interactive shell, we'll do this by:

```
adduser $username -s /sbin/nologin
```

To verify that our user has been created we can run once more, our previously commamd:

```
id $username
```

```
cat /etc/passwd | grep $username 
```

The /etc/passwd stores user accounts information, we can take a look to an output of this command:

```
crazydog:x:502:502::/home/crazydog:/bin/bash
```

Here crazydog has been assigned a UID of 502. It also tell us that the home directory is set to /home/crazydog and that the login shellis set to /bin/bash. The 'x' indicates that shadow passwords are used and that the hashed password is stored in /etc/shadow.

A little comment here, for when we want to get identification data of our operating system, such as information about the distribution, we can take a look to the /etc/os-release file. This one is part of the systemd package, therefore it should be present on all systems running systemd.

```
cat /etc/os-release
```

## Linux SSH Authentication

Let's say that we are using our machine and we frequently log into another system. This process involves having to insert the password each time we want to log into these systems.

As a way of facilitating this, we can use ssh-keygen to generate a public/private authentication key pair. This we'll allow us to log into remote servers without supplying a password. Here We should make an observation that might be important, this if a generate a key pairs as the root user, only the root user will be able to use them.

```
ssh-keygen -t rsa
```

Here with the -t flag we are telling the ssh-keygen command the type of key we want to create. Some of the possible are "rsa1", "dsa", "ecdsa" or "rsa".

This command has a offers a lot of functionalities so you are more than welcomed to google them.

Ok, we are happy, or maybe not, but we have our key. Now what?

Well, we should have a way of providing the key to the remote servers we wish to log in.

This is when the ssh-copy-id command comes into play. ssh-copy-id installs an SSH key as an authorized key in the remote servers.

```
ssh-copy-id -i pathToTheKey user@ipRemoteServer
```

This way we should be able to log into our remote server without being asked to provide a password. The -i flag is used to indicate the key we want to install on the remote server. Another remark is that only the public key is copied to the server. The private key should never be copied to another system.

The -i indicates the identity file to be used. If we don't specify this argument, all the files in the ~/.ssh/ directory with the matching pattern *.pub will be sent.

This command ssh-copy-id can be quite dangerous, so use it with caution.


## Linux Run Levels

At first Run Levels can be difficult to grasp but luckily, they come in numbers.

A run level is a state of init and the whole system that establishes what system services are operating.

When our Linux system boots, the init process is started. This one is responsible to start other start scripts such as those related to the network, or the graphical interface,

What init does is to find the default runlevel of the system in order to execute the corresponding start scripts associated with that default run level.

The run levels can vary depending on the operating system, but the standard Linux kernel supports these seven runlevels:

* 0 - System Halt, i.e., the system can be safely powered off with no activity.
* 1 - Single user mode.
* 2 - Multiple user mode with no Network File System.
* 3 - Multiple user mode under the command line interface and not under the graphical user interface.
* 4 - User-definable
* 5 - Multiple user mode under GUI and this is the standard runlevel for most of the Linux based systems.
* 6 - Reboot which is used to restart the system.

A way to check the default state that our system reaches once if finishes booting is the **systemctl get-default**

```
systemctl get-default
```

This command would return us a target. Where does this data come from? Basically, there is a symbolic link called default.target inside the /etc/systemd/system that points to the indicated target.

Following, we can see that /etc/systemd/system/default.target is a soft link to /lib/systemd/system/multi-user.target :

```
[root@server ~]# systemctl get-default
multi-user.target
[root@server ~]# ls -l /etc/systemd/system/default.target
lrwxrwxrwx 1 root root 37 Jul 10 2020 /etc/systemd/system/default.target -> /lib/systemd/system/multi-user.target
[root@server ~]#
```

If we were to list the current loaded target units, with an active and inactive status, we would use the **systemctl list-units --type=target --all**


```
systemctl list-units --type=target --all

```

A way to modify the default target is by using the systemctl set-default command. For example, we can change to the graphical, which is use to start a graphical session:

```
systemctl set-default graphical.target
```

This procedure does not affect the current system immediately, It would take effect the next timw we boot our system.

## SELinux installation

Let us say that we are in a situation where we are asked to install SELinux as an extra security layer, luckly for us, we don't have to configure it, so we have to its status should be disabled.

But for some of you, just like myself, it might be the first time you here talking about SELinux. What is it?

SELinux, or Security-Enhanced Linux is a security architecture integrated into the kernel using the Linux Security Modules (LSM).

It provides a flexible Mandatory Access Control (MAC) system built into the Linux kernel. Under standard Linux Discretionary Access Control (DAC), an application or process running as a user (UID or SUID) has the user's permissions to objects such as files, sockets, and other processes. Running a MAC kernel protects the system from malicious or flawed applications that can damage or destroy the system.

It's an interesting topic, so I hope to make another entry in the near future, but now let's do what we've been told to do.


We'll, it is a very straightforward process, if we are in red hat based system, like centOS, we'll just run the following command:

```
yum -y install selinux*
```

Then, we can check the existing SELinux status, by executing the *sestatus* command:

```
sestatus
```

As we want it to be disabled and to be this way even after the server reboots, we have to modify the /etc/selinux/config and change SELINUX value to disabled.

Once again, if we run the *sestatus* command, we should get a status of disabled.


## Linux Services

Let's say that the development team has shared with us that the new application release has some dependencias on back end. As a consequence of this, there are some packages/services that need to be installed on all app servers.

Well, in this case the package that we are going to install is the squid package. We also have to make sure that it is enabled to start during boot.

What's the squid package? I'll give the first definition that it appears on google:

```
Squid is a full-featured web proxy cache server application which provides proxy and cache services for Hyper Text Transport Protocol (HTTP), File Transfer Protocol (FTP) and other popular network protocols.
```
Having said this, we'll proceed to the installation. To do this we'll execute the next command (remember that this server is a CentOS):

```
sudo yum install -y squid
```

Next, we'll start the squid service:

```
sudo systemctl start squid
```

And for this service to start when booting we have to enable it:

```
sudo systemctl enable squid
```
To verify/check the status, we run the next command:

```
sudo systemctl status squid
```

Here we should verify, that in the line that starts with *Loaded*, it says enabled, and in the line that starts with *Active*, it says activer (running).

See you in the next entry.

## Linux String Substitute

