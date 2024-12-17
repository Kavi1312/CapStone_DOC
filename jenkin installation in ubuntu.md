# **Jenkins Installation Guide for Ubuntu Server**

This document provides consolidated steps to install and configure Jenkins on an Ubuntu server, including troubleshooting common issues such as **"Package 'jenkins' has no installation candidate"** and GPG key errors.

---

## **Prerequisites**

1. Ubuntu Server (20.04 or 22.04 LTS recommended)
2. Public IP of the server
3. Root or sudo user access
4. Open port **8080** in your firewall or security group
5. Internet connectivity

---

## **Step-by-Step Jenkins Installation**

### **1. Update and Install Required Dependencies**
Update the package lists and install essential tools:

```bash
sudo apt-get update -y
sudo apt-get install -y ca-certificates curl gnupg
```

### **2. Install Java (JDK)**
Jenkins requires Java to run. Install OpenJDK 17:

```bash
sudo apt-get install -y fontconfig openjdk-17-jre
```
Verify Java installation:
```bash
java -version
```

---

### **3. Add the Jenkins GPG Key and Repository**
To install Jenkins securely, add its GPG key and repository:

1. **Create a Keyrings Directory**:
   ```bash
   sudo install -m 0755 -d /etc/apt/keyrings
   ```

2. **Add the Jenkins GPG Key**:
   ```bash
   curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo gpg --dearmor -o /etc/apt/keyrings/jenkins-keyring.gpg
   ```

3. **Add the Jenkins Repository**:
   ```bash
   echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.gpg] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
   ```

---

### **4. Update Package Lists**
Update the package index to include the Jenkins repository:

```bash
sudo apt-get update -y
```

---

### **5. Install Jenkins**
Install the Jenkins package:

```bash
sudo apt-get install -y jenkins
```
If you encounter an error like **"Package 'jenkins' has no installation candidate"**, re-check the repository and GPG key:

- Remove old GPG keys and repositories:
  ```bash
  sudo rm -f /usr/share/keyrings/jenkins-keyring.gpg
  sudo rm -f /etc/apt/sources.list.d/jenkins.list
  ```
- Re-add the correct GPG key and repository:
  ```bash
  curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo gpg --dearmor -o /etc/apt/keyrings/jenkins-keyring.gpg
  echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.gpg] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
  ```
- Update the package list:
  ```bash
  sudo apt-get update -y
  ```
- Install Jenkins:
  ```bash
  sudo apt-get install -y jenkins
  ```

---

### **6. Start and Enable Jenkins Service**
Start the Jenkins service and ensure it runs at boot:

```bash
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

Verify Jenkins status:
```bash
sudo systemctl status jenkins
```

---

### **7. Access Jenkins**
1. Open your browser and navigate to Jenkins using the server's public IP:
   ```
   http://<your-server-ip>:8080
   ```
2. Retrieve the **initial admin password**:
   ```bash
   sudo cat /var/lib/jenkins/secrets/initialAdminPassword
   ```
3. Copy the password, paste it into the Jenkins setup wizard, and follow the on-screen instructions.

---

## **Common Issues and Fixes**

### **Issue: "Package 'jenkins' has no installation candidate"**
- **Cause**: The Jenkins repository or GPG key is missing or invalid.
- **Fix**: Re-add the repository and GPG key:
   ```bash
   sudo rm -f /usr/share/keyrings/jenkins-keyring.gpg
   sudo rm -f /etc/apt/sources.list.d/jenkins.list

   curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo gpg --dearmor -o /etc/apt/keyrings/jenkins-keyring.gpg
   echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.gpg] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
   sudo apt-get update -y
   sudo apt-get install -y jenkins
   ```

### **Issue: "The repository is not signed"**
- **Fix**: Ensure the correct GPG key is used as shown in Step 3.

---

## **Final Script: All-in-One Installation**

If you want to automate the Jenkins installation, save the following script as `install_jenkins.sh` and execute it:

```bash
#!/bin/bash
# Jenkins Installation Script for Ubuntu

# Update and install dependencies
sudo apt-get update -y
sudo apt-get install -y ca-certificates curl gnupg

# Install Java
sudo apt-get install -y fontconfig openjdk-17-jre

# Add Jenkins repository and GPG key
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo gpg --dearmor -o /etc/apt/keyrings/jenkins-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/jenkins-keyring.gpg] https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# Update and install Jenkins
sudo apt-get update -y
sudo apt-get install -y jenkins

# Start and enable Jenkins service
sudo systemctl start jenkins
sudo systemctl enable jenkins

# Output initial admin password
echo "Jenkins Initial Admin Password:"
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

### **How to Run the Script**
1. Save the script to a file:
   ```bash
   nano install_jenkins.sh
   ```
2. Paste the script and save it (`Ctrl+O`, then `Ctrl+X`).
3. Make the script executable:
   ```bash
   chmod +x install_jenkins.sh
   ```
4. Run the script:
   ```bash
   sudo ./install_jenkins.sh
   ```

---

## **Conclusion**
This guide provides clear, step-by-step instructions to install Jenkins on an Ubuntu server, troubleshoot common issues, and verify the installation. Use the provided script to automate the installation process for faster setup.

