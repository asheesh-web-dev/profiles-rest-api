# i want to delete old keys and want to genrate new keys for github personal and github work account . list steps 
### **Step 1: Delete Old SSH Keys**

Before generating new keys, let's clean up your old ones.

1. **Navigate to your SSH key directory**:

   Open Terminal and run:

   ```bash
   cd ~/.ssh
   ```

2. **List all the files in the `.ssh` directory**:

   ```bash
   ls -l
   ```

   You should see something like:

   ```bash
   id_rsa
   id_rsa.pub
   id_rsa_old
   id_rsa_old.pub
   ```

3. **Delete old SSH keys**:

   Delete any keys that you no longer need. For example, if you have old work keys (`id_rsa_old`), delete them:

   ```bash
   rm id_rsa id_rsa.pub
   ```

   **Be careful**: Only delete the keys you don't need. The `id_rsa` and `id_rsa.pub` files are the private and public keys, respectively.

---

### **Step 2: Generate New SSH Keys for GitHub Personal and Work Accounts**

1. **Generate a new key for your GitHub Work Account**:

   Run the following command to generate a new **SSH key** for your **work GitHub account**:

   ```bash
   ssh-keygen -t rsa -b 4096 -C "your_work_email@example.com" -f ~/.ssh/id_rsa_work
   ```

   - Replace `"your_work_email@example.com"` with the email associated with your **work GitHub account**.
   - The `-f ~/.ssh/id_rsa_work` flag will save the key as `id_rsa_work` instead of the default `id_rsa`.

2. **Generate a new key for your GitHub Personal Account**:

   Now, generate a new **SSH key** for your **personal GitHub account**:

   ```bash
   ssh-keygen -t rsa -b 4096 -C "your_personal_email@example.com" -f ~/.ssh/id_rsa_personal
   ```

   - Replace `"your_personal_email@example.com"` with the email associated with your **personal GitHub account**.
   - The `-f ~/.ssh/id_rsa_personal` flag will save the key as `id_rsa_personal`.

---

### **Step 3: Add the SSH Keys to the SSH Agent**

1. **Start the SSH Agent** (if it's not already running):

   ```bash
   eval "$(ssh-agent -s)"
   ```

2. **Add the new work SSH key to the agent**:

   ```bash
   ssh-add ~/.ssh/id_rsa_work
   ```

3. **Add the new personal SSH key to the agent**:

   ```bash
   ssh-add ~/.ssh/id_rsa_personal
   ```

   Now, your SSH agent is managing both keys.

---

### **Step 4: Add Your New Public Keys to GitHub**

You need to add the **public keys** to GitHub for both your **work** and **personal** accounts.

#### 4.1. **Add the Work SSH Key to Your Work GitHub Account**

1. Copy the **public key** for your work account:

   ```bash
   pbcopy < ~/.ssh/id_rsa_work.pub
   ```

2. Log in to your **work GitHub account**.

3. Go to **Settings** > **SSH and GPG keys**.

4. Click **New SSH Key**, paste the key, and give it a title (e.g., "Work Laptop").

5. Click **Add SSH Key**.

#### 4.2. **Add the Personal SSH Key to Your Personal GitHub Account**

1. Copy the **public key** for your personal account:

   ```bash
   pbcopy < ~/.ssh/id_rsa_personal.pub
   ```

2. Log in to your **personal GitHub account**.

3. Go to **Settings** > **SSH and GPG keys**.

4. Click **New SSH Key**, paste the key, and give it a title (e.g., "Personal Laptop").

5. Click **Add SSH Key**.

---

### **Step 5: Configure the SSH Config File**

To ensure that the right SSH key is used for each account, you'll configure the SSH client to use different keys for your **work** and **personal** GitHub accounts.

1. **Open your SSH config file** for editing:

   ```bash
   nano ~/.ssh/config
   ```

2. **Add the following configuration**:

   ```bash
   # Work GitHub account
   Host github.com-work
     HostName github.com
     User git
     IdentityFile ~/.ssh/id_rsa_work
     IdentitiesOnly yes

   # Personal GitHub account
   Host github.com-personal
     HostName github.com
     User git
     IdentityFile ~/.ssh/id_rsa_personal
     IdentitiesOnly yes
   ```

   - The `Host` entries (`github.com-work` and `github.com-personal`) are aliases that you will use when cloning or interacting with repositories.
   - This tells SSH to use the correct private key (`id_rsa_work` or `id_rsa_personal`) based on the alias you use in your Git commands.

3. **Save and close** the file (`Ctrl + O`, then `Enter`, then `Ctrl + X`).

---

### **Step 6: Test the SSH Connections**

To make sure your SSH setup is correct, test the connection to both GitHub accounts.

#### 6.1. **Test SSH for Work Account**

```bash
ssh -T git@github.com-work
```

You should see a message like this:

```
Hi your_work_username! You've successfully authenticated, but GitHub does not provide shell access.
```

#### 6.2. **Test SSH for Personal Account**

```bash
ssh -T git@github.com-personal
```

You should see a message like this:

```
Hi your_personal_username! You've successfully authenticated, but GitHub does not provide shell access.
```

---

### **Step 7: Clone Repositories Using the Correct SSH URL**

Now that everything is set up, use the aliases defined in your `~/.ssh/config` to clone repositories.

#### 7.1. **Clone a Work Repository**

For your **work repository**, use the `github.com-work` alias:

```bash
git clone git@github.com-work:DA-NUA/apps.git
```

#### 7.2. **Clone a Personal Repository**

For your **personal repository**, use the `github.com-personal` alias:

```bash
git clone git@github.com-personal:your_personal_username/repository.git
```

---

### **Step 8: (Optional) Set Git Config for Each Account**

You might want to set a different **Git username** and **email** for each repository.

#### 8.1. **Set Git Config for Work Repository**

Navigate to your **work repository** and run the following commands:

```bash
git config user.name "Your Work Name"
git config user.email "your_work_email@example.com"
```

#### 8.2. **Set Git Config for Personal Repository**

Navigate to your **personal repository** and run the following commands:

```bash
git config user.name "Your Personal Name"
git config user.email "your_personal_email@example.com"
```

---

### **Recap of the Steps:**

1. **Delete old SSH keys**.
2. **Generate new SSH keys** for both work and personal GitHub accounts.
3. **Add both public keys to GitHub** (work and personal).
4. **Configure SSH to use the correct key for each account** in the `~/.ssh/config` file.
5. **Test SSH connections** to ensure everything is working.
6. **Clone repositories** using the appropriate SSH alias.
7. **(Optional)** Set different Git configuration for each repository (name and email).

---

That’s it! You now have a clean setup for using **multiple GitHub accounts** (work and personal) on the same machine with different SSH keys. Let me know if you need further assistance!

# so if i am not adding keys to ssh-agent then every time i have to pass passphrase while using private key 
Yes, that's correct!

### If You **Don't Add Keys to the SSH Agent**:
If you're not using the `ssh-agent` to manage your SSH keys, you will be prompted for the **passphrase** every time you use a private key (e.g., for operations like `git push`, `git pull`, etc.) and the key has a passphrase.

### Here's Why:

- **SSH Keys with Passphrases**: If your private key is protected by a passphrase, you’ll need to enter it to unlock the key each time you use it.
- **Without `ssh-agent`**: The SSH agent is responsible for caching your passphrase, so if you’re not using it, you’ll be asked for the passphrase every time.

### Using `ssh-agent` to Avoid Entering the Passphrase Every Time
The `ssh-agent` can store your private key in memory, so you don’t have to repeatedly enter your passphrase during your session. When you add your key to the agent, it remembers the passphrase until you close the terminal or logout.

### Steps to Add SSH Keys to the SSH Agent:

1. **Start the SSH Agent**:
   Run the following command to start the agent (if it isn't already running):

   ```bash
   eval "$(ssh-agent -s)"
   ```

2. **Add your SSH key to the agent**:
   Add your SSH key (e.g., `id_rsa_work` or `id_rsa_personal`) to the agent:

   ```bash
   ssh-add ~/.ssh/id_rsa_work
   ```

   (Replace `~/.ssh/id_rsa_work` with the correct key filename if necessary.)

   You’ll be prompted to enter the **passphrase** **once**. After that, the agent will remember the key and passphrase for the duration of your terminal session.

3. **Optional**: If you want the SSH agent to remember your passphrase across system restarts, you can add the following to your shell's configuration file (e.g., `~/.zshrc` or `~/.bash_profile`):

   ```bash
   eval "$(ssh-agent -s)"
   ssh-add -K ~/.ssh/id_rsa_work
   ```

   - The `-K` option (on macOS) stores the key in the macOS keychain, so you don't have to enter the passphrase when starting a new session.

---

### To Summarize:

- **Without `ssh-agent`**: You will be prompted for your passphrase every time you use the key.
- **With `ssh-agent`**: You enter the passphrase **once**, and the agent will store it for the duration of the session, so you don't have to enter it repeatedly.

If you want the most seamless experience, I recommend using the `ssh-agent`. It will make working with your SSH keys much easier, especially if you have a passphrase on your keys.