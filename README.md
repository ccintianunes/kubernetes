# kubernetes
Guia para criar um cluster kubernetes com Ansible, utilizando o projeto Kubespray.

## Crie um par de chave no orquestrador utilizando o usuário root, caminho padrão e sem senha de criptografia.
Como no exemplo a seguir, basta executar o comando `ssh-keygen` 

## Enviando a chave pública para nodes
Para usar este comando que envia a chave pública é necessário habilitar o acesso por senha na maquina que receberá a chave: 
```
ssh-copy-id 'ip da máquina  destino'
```
# Clone the kubespray Project
```
git clone https://github.com/kubernetes-sigs/kubespray.git
```

# Install and activate python virtual environment
```
apt install python3-venv
python3 -m venv kubespray-venv
source kubespray-venv/bin/activate

```

# Install ansible 
## Instalar bibliotecas python necessárias a partir do arquivo de texto
```
cd kubespray
pip install -U -r requirements.txt
```

# Create hosts inventory
```
declare -a IPS=(172.20.0.21 172.20.0.22 172.20.0.23 172.20.0.24 172.20.0.25)
cd ../ 
mkdir cluster
CONFIG_FILE=cluster/hosts.yaml python3 kubespray/contrib/inventory_builder/inventory.py ${IPS[@]}
```

# Inspect hosts.yaml
```
cat cluster/hosts.yaml
```

# Create file cluster/cluster-config.yaml
inserir no arquivo:
```
echo "cluster_name: homelab-k8s" > cluster/cluster-config.yaml
echo "kube_version: v1.26.5" >> cluster/cluster-config.yaml
```

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
