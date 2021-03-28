## Week 4 Homework: Linux Systems Administration


#### Step 1: Ensure Permissions on Sensitive Files

The `/etc/` directory is where system configuration files exist. Start by navigating to this directory with `cd /etc/`.

Inspect the file permissions of each of the files below.  This should have already been completed in the activity, but let's double check!
If they do not match the descriptions, please update the permissions.

  1. Permissions on `/etc/shadow` should allow only `root` read and write access.

sudo chmod 600 /etc/shadow 


  2. Permissions on `/etc/gshadow` should allow only `root` read and write access.

sudo chmod 600 /etc/shadow

  3. Permissions on `/etc/group` should allow `root` read and `write` access, and allow everyone else `read` access only.

sudo chmod 644 /etc/group


  4. Permissions on `/etc/passwd` should allow `root` read and `write` access, and allow everyone else `read` access only.

sudo chmod 644 /etc/passwd


#### Step 2: Create User Accounts

This step asks you to set up various users. These commands do not require you to be working from a specific directory.

1. Add user accounts for `sam`, `joe`, `amy`, `sara`, and `admin`.

sudo adduser <name>   

2. We want to make sure that only the `admin` user has general `sudo` group access. This requires a command that will allow user modifications.

sudo usermod -aG sudo admin 

sudo -lU admin 

#### Step 3: Create User Group and Collaborative Folder

Now we want to execute the commands to fully set up a group on our system.

This requires us to create a group, add users to it, create a shared group folder, set the group folder owners for these shared folders.

1. Add the group `engineers` to the system.

sudo addgroup engineers

2. Add users `sam`, `joe`, `amy`, and `sara` to the managed group. This will be similar to how you added `admin` to the `sudo` group in the previous exercise.

sudo usermod -G engineers <name>

3. Create a shared folder for this group: `/home/engineers`.

sudo mkdir engineers 

4. Change ownership on the new engineers' shared folder to the `engineers` group.

sudo chown engineers /home/engineers


#### Step 4: Lynis Auditing

The final step on your administrator's list involves running an audit against the system in order to harden it. You'll use the system and security auditing tool Lynis to do so.

1. Install the Lynis package to your system if it is not already installed.

sudo apt install lynis 

2. Check the Lynis documentation for instructions on how to run a system audit.

sudo lynis 

3. Run a Lynis system audit with `sudo`.

sudo lynis audit system

4. Provide a report from the Lynis output on what more could be done to harden the system.

sudo cat lynis-report.dat (lynis report)

sudo cat lynis.log (test and debug information)


#### Bonus 

Despite claims from enthusiasts, Linux is _not_ immune to malware. You will need to install and run the application chkrootkit, to search for any potential rootkits installed on the system.

1. Install the chkrootkit package to your system if it is not already installed.

sudo apt install chkrootkit

2. Check the chkrootkit documentation for instructions on how to run a scan to find system root kits.

man chkrootkit

3. Run chkrootkit (with `sudo`) in expert mode to verify the system does not have a root kit installed.

sudo chkrootkit -x 

4. Provide a report from chkrootkit output on what more could be done to harden the system.

sudo chkrootkit > log_results

less log_results


---
© 2020 Trilogy Education Services, a 2U, Inc. brand. All Rights Reserved.
