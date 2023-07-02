# kubernetes
# Clone the kubespray Project
```
git clone https://github.com/kubernetes-sigs/kubespray.git
```

# Install ansible 
```
cd kubespray
pip install -U -r requirements-2.12.txt
```

# Create hosts inventory
```
declare -a IPS=(172.20.0.21 172.20.0.22 172.20.0.23 172.20.0.24 172.20.0.25)
cd ../ 
mkdir -p cluster/homelab-k8s
CONFIG_FILE=cluster/hosts.yaml python3 kubespray/contrib/inventory_builder/inventory.py ${IPS[@]}
```

# Inspect hosts.yaml
```
cat cluster/hosts.yaml
```

# Create file /cluster/cluster-config.yaml

# Reset cluster
```
cd kubespray
ansible-playbook -i ../cluster/hosts.yaml -e @../cluster/cluster-config.yaml --user=root --become --become-user=root reset.yml
```

# Deploy cluster
```
cd kubespray
ansible-playbook -i ../cluster/hosts.yaml -e @../cluster/cluster-config.yaml --user=root --become --become-user=root cluster.yml
```

# Verify deployment
```
ssh root@172.20.0.21
sudo -i 
kubectl get nodes
kubectl -n kube-system get pods
less /etc/kubernetes/admin.conf 
```

# Upgrade Cluster
```
ansible-playbook -i ../cluster/hosts.yaml -e @../cluster/cluster-config.yaml --user=root --become --become-user=root upgrade-cluster.yml
```

# Scale Down Cluster
```
ansible-playbook -i ../cluster/hosts.yaml -e @../cluster/cluster-config.yaml --user=root --become --become-user=root remove-node.yaml -e node=node5
```


# Scale Up cluster
```
ansible-playbook -i ../clusters/hosts.yaml -e @../clusters/cluster-config.yaml --user=root --become --become-user=root scale.yaml --limit=node5
```
