🚀 Automating Remote Pod Configuration in Kubernetes using Ansible over SSH

Ever wondered if you could treat a Kubernetes pod like a remote VM?

Well, I recently built a setup where an Ansible control pod inside the cluster securely SSHs into a target pod running Apache and performs real-time configuration — just like managing a remote server! 💻🔧

🧱 Tech Stack Highlights:

Docker: Custom images for both Ansible and Apache pods.

Kubernetes: Pods communicate over NodePort; no Ingress required.

Ansible: Executes a playbook from one pod to another using SSH.

Apache + OpenSSH: Combined in one pod for web serving and remote access.

🔄 What’s Happening?

A custom index.html file is pushed to the Apache pod's web root using Ansible.

All of this is triggered from within the cluster — no external IP or jump box involved.

And the best part? Apache doesn't even need a restart. Instant updates! ⚡

📁 Project Structure Includes:

Dockerfiles (Ansible + Apache)

Kubernetes YAMLs

Ansible inventory + playbook

SSH service wiring inside the cluster

🔍 Real-world use case? Imagine pods dynamically updating other pods during runtime — say for agent-based patching, live content updates, or fleet-wide custom configurations in a controlled setup.

📦 GitHub repo https://github.com/Sayantan2k24/LW-Project-07-Ansible-Driven-Configuration-Management-Inside-Kubernetes-Pod-apache.git


#DevOps #Kubernetes #Ansible #Docker #CloudNative #OpenSource #Automation #K8s #CI_CD