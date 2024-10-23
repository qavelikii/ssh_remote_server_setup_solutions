# SSH Remote Server Setup

[roadmap.sh/projects/](https://roadmap.sh/projects/ssh-remote-server-setup)

This guide provides step-by-step instructions for setting up a remote Linux server and configuring it to allow SSH access. Follow each step to successfully complete the setup.

## Step 1: Connect to Your Server

1. **Access the VPS Control Panel**:
   - Go to your VPS control panel URL and log in with your credentials.

2. **Connect to Your Server via SSH**:
   - Open your terminal (use PuTTY on Windows, or the default terminal on Mac/Linux).
   - Enter the following command to connect:
     ```bash
     ssh root@<your-server-ip>
     ```
   - Enter the password provided by your hosting service.

## Step 2: Create Two New SSH Keys

1. **Generate the First SSH Key Pair**:
   - Run the following command in your terminal:
     ```bash
     ssh-keygen -t rsa -b 4096 -C "First SSH key for server"
     ```
   - You will be prompted to choose a location to save the key. You can accept the default (`/root/.ssh/id_rsa`) or specify your own path.
   - Set a passphrase for added security (optional).

2. **Generate the Second SSH Key Pair**:
   - Run a similar command, but specify a different filename for the key:
     ```bash
     ssh-keygen -t rsa -b 4096 -C "Second SSH key for server" -f ~/.ssh/id_rsa_second
     ```
   - Again, set a passphrase for added security (optional).

## Step 3: Add SSH Keys to the Server

1. **Copy Public Keys to the Server**:
   - Before copying the keys, ensure that the server allows password authentication. Edit the SSH configuration file:
     ```bash
     nano /etc/ssh/sshd_config
     ```
   - Find the line with `PasswordAuthentication` and set it to `yes` if not already set:
     ```
     PasswordAuthentication yes
     ```
   - Save the file and restart the SSH service:
     ```bash
     systemctl restart sshd
     ```
   - Use the following command to copy the first public key to the server:
     ```bash
     ssh-copy-id -i ~/.ssh/id_rsa.pub root@<your-server-ip>
     ```
   - Repeat the process for the second key:
     ```bash
     ssh-copy-id -i ~/.ssh/id_rsa_second.pub root@<your-server-ip>
     ```

2. **Verify SSH Connection Using Keys**:
   - Connect to your server using the first key:
     ```bash
     ssh -i ~/.ssh/id_rsa root@<your-server-ip>
     ```
   - Connect using the second key:
     ```bash
     ssh -i ~/.ssh/id_rsa_second root@<your-server-ip>
     ```

## Step 4: Configure SSH Configuration File

1. **Open or Create SSH Config File**:
   - Run the following command to open the SSH configuration file:
     ```bash
     nano ~/.ssh/config
     ```
   - Add the configuration for the first key:
     ```
     Host server1
         HostName <your-server-ip>
         User root
         IdentityFile ~/.ssh/id_rsa
     ```
   - Add the configuration for the second key:
     ```
     Host server2
         HostName <your-server-ip>
         User root
         IdentityFile ~/.ssh/id_rsa_second
     ```

2. **Save and Exit the File** (Ctrl + O to save, then Enter, and Ctrl + X to exit).

3. **Connect Using Aliases**:
   - To connect using the first key:
     ```bash
     ssh server1
     ```
   - To connect using the second key:
     ```bash
     ssh server2
     ```

## Step 5: Install and Configure Fail2ban (Stretch Goal)

1. **Install Fail2ban**:
   - Run the following command to install Fail2ban:
     ```bash
     dnf install fail2ban -y
     ```

2. **Start and Configure Fail2ban**:
   - Start the Fail2ban service:
     ```bash
     systemctl start fail2ban
     ```
   - Edit the Fail2ban configuration file to protect against brute force attacks:
     ```bash
     nano /etc/fail2ban/jail.local
     ```
   - Add basic settings to enable SSH protection:
     ```
     [sshd]
     enabled = true
     port = 22
     logpath = /var/log/secure
     maxretry = 5
     ```
   - Save the file and restart Fail2ban:
     ```bash
     systemctl restart fail2ban
     ```

## Conclusion

You have successfully set up a remote server on AlmaLinux 8, added two SSH keys for secure access, and configured the SSH configuration file for easy alias connections. Additionally, Fail2ban was installed to help protect against brute force attacks. Create a `README.md` file with these steps (excluding any private information and credentials) for documentation purposes.


