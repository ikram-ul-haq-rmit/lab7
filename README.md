# Clone the Repo cosc2759-sem1-2023-labs
git clone https://github.com/rmit-computing-technologies/cosc2759-sem1-2023-labs
cd lab07
cd infra
terraform init
vim you.auto.tfvars
# update public key (/home/ubuntu/.ssh/github_sdo_key.pub) and ip address and set allow=true i.e allow all ips
# Check IP address with "curl ifconfig.co"
stat -c "%a" /home/ubuntu/.ssh/github_sdo_key.pub
chmod 400 /home/ubuntu/.ssh/github_sdo_key.pub
terraform apply

# "app" = "public_ip_address" = "3.87.44.32"
# "db" = "public_ip_address" = "44.208.36.128"

# Update app_server IP address in inventory1.yml file. Then run below command
ansible app_servers -m ping -i inventory1.yml -u ubuntu --private-key ~/.ssh/github_sdo_key

# Run the “app” playbook:
ansible-playbook app-playbook.yml -i inventory1.yml --private-key ~/.ssh/github_sdo_key

curl http://3.87.44.32/

# Update RDS IP in inventory2.yml
vim inventory2.yml
ansible-playbook db-playbook.yml -i inventory2.yml --private-key ~/.ssh/github_sdo_key

# 7.0
docker pull postgres:14.7
docker run -it --rm postgres:14.7 psql -h 44.208.36.128 -U pete -d foo      #Password devops

# 8.0: Create Tables
CREATE TABLE bars (id INT PRIMARY KEY, name TEXT, length TEXT);

# Insert 2 rows
INSERT INTO bars (id, name, length) VALUES (1, 'Mars Bar','8cm');
INSERT INTO bars (id, name, length) VALUES (2, 'Snickers Bar','10cm');

# 9: Query
SELECT * FROM bars;

# Apply common configuration to both servers

# Update app and db server IP in inventory3.yml
ansible all_servers -m ping -i inventory3.yml -u ubuntu --private-key ~/.ssh/github_sdo_key

# Run the “compliance” playbook:
ansible-playbook compliance-playbook.yml -i inventory3.yml --private-key ~/.ssh/github_sdo_key

# SSH to app instance
ssh -i ~/.ssh/github_sdo_key ubuntu@3.87.44.32
# Then run "cowsay hello"

# SSH to app instance
ssh -i ~/.ssh/github_sdo_key ubuntu@44.208.36.128
# Then run "cowsay hello"

cd ../infra/
# Destroy the created resources
terraform destroy
