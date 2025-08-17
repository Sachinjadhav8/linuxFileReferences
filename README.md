# linuxFileReferences

secure way with SSH keys per user (industry best practice).

That way each user will log in with their own username + their own SSH key, instead of a shared key or password.

🔹 Step 1: Create new users

On your EC2 instance, create users for each person:

sudo adduser karan
sudo adduser devuser

🔹 Step 2: Give each user a home .ssh directory

For each user, create the .ssh folder and set permissions:

sudo mkdir /home/karan/.ssh
sudo chmod 700 /home/karan/.ssh
sudo chown karan:karan /home/karan/.ssh


Repeat for devuser:

sudo mkdir /home/devuser/.ssh
sudo chmod 700 /home/devuser/.ssh
sudo chown devuser:devuser /home/devuser/.ssh

🔹 Step 3: Collect their public SSH keys

Each user must generate a key pair on their own computer (not on the server):

ssh-keygen -t rsa -b 4096 -C "karan@example.com"


This creates:

Private key → ~/.ssh/id_rsa (they keep this safe, never share it)

Public key → ~/.ssh/id_rsa.pub (they give this to you)

🔹 Step 4: Add public keys to their accounts

For example, if Karan gives you his id_rsa.pub, put it in:

sudo nano /home/karan/.ssh/authorized_keys


Paste his public key inside.

Then fix permissions:

sudo chmod 600 /home/karan/.ssh/authorized_keys
sudo chown karan:karan /home/karan/.ssh/authorized_keys


Do the same for each user with their key.

🔹 Step 5: Test login

Now Karan can log in like this from his machine:

ssh -i ~/.ssh/id_rsa karan@<ec2-public-ip>


And devuser with his own key:

ssh -i ~/.ssh/id_rsa devuser@<ec2-public-ip>

🔹 Step 6 (Optional): Give sudo access

If you want them to run admin commands:

sudo usermod -aG sudo karan
sudo usermod -aG sudo devuser
