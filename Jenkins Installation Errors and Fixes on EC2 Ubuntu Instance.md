# Jenkins Installation Errors and Fixes on EC2 Ubuntu Instance

## Table of Contents
1. [Error 1: GPG Key Error (`NO_PUBKEY`)](#error-1-gpg-key-error-nopubkey)
2. [Error 2: `Package 'jenkins' has no installation candidate`](#error-2-package-jenkins-has-no-installation-candidate)
3. [Error 3: Corrupted `.deb` File](#error-3-corrupted-deb-file)
4. [Error 4: `Failed to start jenkins.service: Unit jenkins.service not found`](#error-4-failed-to-start-jenkinsservice-unit-jenkinsservice-not-found)
5. [Summary of Commands](#summary-of-commands)
6. [Verification Steps](#verification-steps)

---

## **Error 1: GPG Key Error (`NO_PUBKEY`)**

### Error Message
```
W: GPG error: https://pkg.jenkins.io/debian-stable binary/ Release: The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 5BA31D57EF5975CA
E: The repository 'https://pkg.jenkins.io/debian-stable binary/ Release' is not signed.
```

### Cause
The Jenkins repository GPG key was missing or outdated.

### Fix
Run these commands to remove the invalid key, add the updated GPG key, and reconfigure the repository:

```bash
sudo rm -f /etc/apt/keyrings/jenkins-keyring.gpg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo gpg --dearmor -o /etc/apt/keyrings/jenkins-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.gpg] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y
```

---

## **Error 2: `Package 'jenkins' has no installation candidate`**

### Error Message
```
E: Package 'jenkins' has no installation candidate
```

### Cause
The Jenkins repository was not configured properly or was not recognized due to a missing GPG key.

### Fix
Re-add the repository and update package lists:

```bash
sudo rm -f /etc/apt/sources.list.d/jenkins.list
sudo rm -f /etc/apt/keyrings/jenkins-keyring.gpg
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo gpg --dearmor -o /etc/apt/keyrings/jenkins-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.gpg] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get clean
sudo apt-get update -y
sudo apt-get install -y jenkins
```

---

## **Error 3: Corrupted `.deb` File**

### Error Message
```
dpkg-deb (subprocess): cannot copy archive member from 'jenkins_2.387.2_all.deb' to decompressor pipe: unexpected end of file or stream
dpkg: error processing archive jenkins_2.387.2_all.deb (--install): unexpected end of file or stream
```

### Cause
The `.deb` file for Jenkins was not fully downloaded, leading to corruption.

### Fix
Delete the corrupted file and re-download it using `wget` with the `-c` (resume) flag:

```bash
rm -f jenkins_2.387.2_all.deb
wget -c --show-progress https://pkg.jenkins.io/debian-stable/binary/jenkins_2.387.2_all.deb
sudo dpkg -i jenkins_2.387.2_all.deb
sudo apt-get install -f -y
```

---

## **Error 4: `Failed to start jenkins.service: Unit jenkins.service not found`**

### Error Message
```
Failed to start jenkins.service: Unit jenkins.service not found.
Failed to enable unit: Unit file jenkins.service does not exist.
```

### Cause
The Jenkins installation was incomplete, and the `jenkins.service` unit file was not created.

### Fix
Reinstall Jenkins and ensure dependencies are resolved:

```bash
sudo apt-get update -y
sudo apt-get install -f -y  # Fix broken dependencies
sudo apt-get install -y jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
sudo systemctl status jenkins
```

---

## **Summary of Commands**
Here’s the full list of commands used to fix all issues:

```bash
# Remove old repository and GPG key
sudo rm -f /etc/apt/sources.list.d/jenkins.list
sudo rm -f /etc/apt/keyrings/jenkins-keyring.gpg

# Add updated GPG key and repository
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo gpg --dearmor -o /etc/apt/keyrings/jenkins-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.gpg] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# Update package lists
sudo apt-get clean
sudo apt-get update -y

# Fix corrupted .deb installation (if applicable)
rm -f jenkins_2.387.2_all.deb
wget -c --show-progress https://pkg.jenkins.io/debian-stable/binary/jenkins_2.387.2_all.deb
sudo dpkg -i jenkins_2.387.2_all.deb
sudo apt-get install -f -y

# Install Jenkins
sudo apt-get install -y jenkins

# Start and enable Jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
sudo systemctl status jenkins
```

---

## **Verification Steps**

### Verify Jenkins Installation
Check the Jenkins service:

```bash
sudo systemctl status jenkins
```

### Open Jenkins in Your Browser
Access Jenkins at:

```
http://<your-server-ip>:8080
```

### Retrieve the Initial Admin Password
Run the following command to get the admin password:

```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

---

## **Conclusion**
By following these steps, you have resolved:

- GPG key errors (`NO_PUBKEY` issue).
- Missing Jenkins package issues.
- Corrupted `.deb` file problems.
- Missing `jenkins.service` startup errors.

Let me know if you need any further clarification! 🚀

