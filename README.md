# 🧰 CIT 4640 – Intro to Ansible Lab

This project uses **Terraform** to provision two AWS EC2 instances and **Ansible** to configure them with Nginx.
By the end, you’ll be able to visit a web page served from both instances showing system information rendered via a Jinja2 template.

---

## 1. Create a New SSH Key Pair

```bash
ssh-keygen -t rsa -b 4096 -f ~/.ssh/aws -N ""
chmod 600 ~/.ssh/aws
```

**Description:**  
Creates a new SSH key pair named `aws` inside `~/.ssh` directory.  
The private key (`aws`) will be used by Ansible; the public key (`aws.pub`) will be uploaded to AWS.

---

## 2. Import Public Key to AWS

```bash
./scripts/import_lab_key ~/.ssh/aws.pub
```

**Description:**  
Uses the provided script to import the local public key to AWS under the name `4640-wk7-key`.  
Terraform will reference this key when launching EC2 instances.

---

## 3. Terraform Commands

```bash
cd terraform
terraform init
terraform fmt
terraform validate
terraform plan
terraform apply
```

**Descriptions:**
- `init` → Downloads AWS provider and sets up the working directory.
- `fmt` → Formats Terraform code to standard style. 
- `validate` → Checks syntax and configuration for errors. 
- `plan` → Previews infrastructure changes before applying.
- `apply` → Builds the actual AWS infrastructure (creates 2 EC2 instances).

Once applied, Terraform will output each instance’s **public IP** and **DNS name**.

---

## 4. Configure Ansible Inventory

Edit `ansible/inventory/hosts.yml` and replace placeholders with instance addresses:

```yaml
all:
  children:
    web:
      hosts:
        server-one:
          ansible_host: ec2-34-221-221-250-us-west-2.compute.amazonaws.com
        server-two:
          ansible_host: ec2-35-163-70-16.us-west-2.compute.amazonaws.com
```

---

## 5. Run Ansible

### Syntax Check

```bash
cd ansible
ansible-playbook playbook.yml --syntax-check
```

### Run the Playbook

```bash
ansible-playbook playbook.yml
```

**Description:**  
Runs tasks to:
- Install **nginx**
- Create `/web/html` directory
- Copy **nginx.conf** file
- Enable the config via symlink
- Generate an `index.html` file from Jinja2 template
- Reload and enable the nginx service

---

## 6. Verify in Browser

Open one EC2 instance IPs or DNS names:

```
http://34.221.221.250
```


---

## 7. Cleanup

### Destroy AWS Infrastructure

```bash
cd terraform
terraform destroy
```

### Delete Key Pair from AWS

```bash
./scripts/delete_lab_key
```

### (Optional) Remove Local Keys

```bash
rm ~/.ssh/aws ~/.ssh/aws.pub
```

---

## Screenshot



---

## Project Structure

```
intro-to-ansible-lab-files/
├── ansible/
│   ├── ansible.cfg
│   ├── inventory/
│   │   └── hosts.yml
│   ├── files/
│   │   └── nginx.conf
│   ├── templates/
│   │   └── index.html.j2
│   └── playbook.yml
├── scripts/
│   ├── import_lab_key
│   └── delete_lab_key
├── terraform/
│   └── main.tf
└── README.md
```

---

##  Demo Checklist

-  Repo cloned  
-  SSH key created and imported  
-  Terraform infrastructure deployed  
-  Ansible playbook executed successfully  
-  Web page verified in browser  
-  Terraform destroyed  
-  Key deleted from AWS  

---

