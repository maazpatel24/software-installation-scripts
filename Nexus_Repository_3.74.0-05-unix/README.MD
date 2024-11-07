# Nexus Installation Script
This script automates the installation and setup of the Nexus Repository Manager on an Ubuntu server. It ensures that the Nexus service is properly configured and running as a systemd service.

## Features
- Installs **OpenJDK 11**, a prerequisite for running Nexus.
- Downloads and extracts the specified version of Nexus.
- Configures Nexus to run as a dedicated user (nexus).
- Sets up Nexus as a systemd service for easy management.
- Validates the service status and reports its state.

## Prerequisites
1. A server running Ubuntu.
2. Sudo/root privileges to execute the script.

## Usage
1. Download the script: Save the script to a file, e.g., `install_nexus.sh`.

2. Make it executable:

    ```bash
    chmod +x install_nexus.sh
    ```

3. Run the script with sudo:

    ```bash
    sudo ./install_nexus.sh
    ```

## Script Workflow

1. Environment Check:

    - Ensures the script is executed with root privileges.

2. System Updates and JDK Installation:

    - Updates the package list.
    - Installs OpenJDK 11 to meet Nexus's Java dependency.
  
3. User Setup:

    - Checks if the `nexus` user exists. If not, creates it.

4. Nexus Download and Installation:

    - Downloads the Nexus tarball from Sonatype's official site.
    - Extracts the archive and removes the tarball.
    - Renames the extracted directory to `nexus`.

5. Ownership and Configuration:

    - Sets ownership of the `nexus` files to the `nexus` user.
    - Updates the `nexus.rc` file to specify the `nexus` user.

6. Systemd Service Setup:

    - Creates the `nexus.service` file in `/etc/systemd/system/`.
    - Configures the service to start Nexus, manage its state, and restart on failure.

7. Service Management:

    - Reloads systemd to apply the new service.
    - Enables the Nexus service to start on boot.
    - Starts the Nexus service.

8. Service Status Check:

    - Checks if the Nexus service is running.
    - If not active, it retrieves and displays its current status for troubleshooting.

## Output Example
- Service Active:

    ```plaintext
    Nexus service is active and running.
    ```

- Service Not Active:

    ```plaintext
    Nexus service is not active. Checking its status...
    Active: failed (Result: exit-code) since ...
    ```
## Post-Installation
- Access the Nexus web interface at:

```arduino
http://<your-server-ip>:8081
```

- Default login credentials:
    - **Username**: `admin`
    - **Password**: Located in the `admin.password` file within `/opt/sonatype-work/`.
  
## Troubleshooting
If the Nexus service fails to start:

1. Check the service logs:
    ```bash
    sudo journalctl -xeu nexus.service
    ```
2. Verify the Nexus logs:
    ```bash
    cat /opt/sonatype-work/nexus3/log/nexus.log
    ```



<!-- ---
# Nexus Installation and Service Setup (Manual Steps)

```bash
# Update the package list to ensure the latest package information is fetched
sudo apt update
```
```bash
# Install OpenJDK 11 for running Nexus (Java-based application)
sudo apt install openjdk-11-jdk -y
```
```bash
# Verify that Java has been successfully installed by checking the version
java -version
```
```bash
# Switch to the root user to have the necessary privileges for system-wide changes
sudo su
```
```bash
# Change directory to /opt where Nexus will be installed
cd /opt/
```
```bash
# Download Nexus from the official Sonatype repository (adjust the version if needed)
wget https://download.sonatype.com/nexus/3/nexus-3.74.0-05-unix.tar.gz
```
```bash
# Extract the Nexus tar.gz file and remove the original archive to save space
tar -xvzf nexus-3.74.0-05-unix.tar.gz && rm nexus-3.74.0-05-unix.tar.gz
```
```bash
# Rename the extracted folder to 'nexus' for easier reference
mv nexus-3.74.0-05-unix nexus
```
```bash
# Create a new system user 'nexus' for running Nexus and give it sudo privileges without a password prompt
useradd -M -d /opt/nexus -s /bin/bash nexus && echo "nexus ALL=(ALL) NOPASSWD: ALL" | sudo tee -a /etc/sudoers
```
```bash
# Change ownership of the Nexus files and the Sonatype work directory to the 'nexus' user
chown -R nexus:nexus /opt/nexus /opt/sonatype-work
```
```bash
# Update the Nexus configuration to specify the 'nexus' user for running the Nexus service
sed -i 's/#run_as_user=""/run_as_user="nexus"/' /opt/nexus/bin/nexus.rc
```
```bash
# Create a systemd service file for Nexus to run it as a service and manage it like other system services
bash -c 'cat > /etc/systemd/system/nexus.service <<EOF
[Unit]
Description=nexus service
After=network.target

[Service]
Type=forking
LimitNOFILE=65536
ExecStart=/opt/nexus/bin/nexus start
ExecStop=/opt/nexus/bin/nexus stop
User=nexus
Restart=on-abort
TimeoutSec=600

[Install]
WantedBy=multi-user.target
EOF'
```
```bash
# Reload systemd to pick up the new Nexus service configuration
systemctl daemon-reload

# Enable the Nexus service to start automatically on system boot
systemctl enable nexus.service

# Start the Nexus service
systemctl start nexus.service

# Check the status of the Nexus service to ensure it's running correctly
systemctl status nexus.service
``` -->