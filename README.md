# SUPMAP
Contexte du projet :
La société fictive Trafine souhaite développer une application de navigation en temps réel, similaire à Waze, qui permettrait aux utilisateurs de recevoir des informations sur la circulation, les accidents et les obstacles.

Cette application devra également permettre aux utilisateurs de signaler des incidents et de recevoir des itinéraires optimisés en fonction des conditions de circulation. 

Le projet devra inclure une application mobile pour les utilisateurs, une interface web pour la gestion et la visualisation des données en temps réel, ainsi qu'une architecture de micro-services pour gérer les différentes fonctionnalités. 

Pour la bêta, Trafine souhaite se concentrer sur les trajets en France métropolitaine. 

 
# SUPMAP - Documentation

## Commandes pour Lancer l'Environnement

### Étape 1 : Initialisation du Cluster Kubernetes
1. **Initialiser le Cluster sur le Nœud Master** :
   ```bash
   sudo swapoff -a
   sudo sed -i '/ swap / s/^/#/' /etc/fstab
   cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
   net.ipv4.ip_forward = 1
   EOF
   sudo sysctl --system
   cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
   overlay
   br_netfilter
   EOF
   sudo modprobe overlay
   sudo modprobe br_netfilter
   sudo apt-get update
   sudo apt-get -y install ca-certificates curl
   sudo install -m 0755 -d /etc/apt/keyrings
   sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
   sudo chmod a+r /etc/apt/keyrings/docker.asc
   sudo apt-get -y install containerd.io
   sudo mkdir -p /etc/containerd
   sudo containerd config default | sudo tee /etc/containerd/config.toml
   sudo apt-get update
   sudo apt-get install -y apt-transport-https ca-certificates curl gpg
   curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
   sudo apt-get update
   sudo apt-get install -y kubelet kubeadm kubectl
   sudo apt-mark hold kubelet kubeadm kubectl
   sudo kubeadm init --pod-network-cidr=192.168.0.0/16
   ```

2. **Configurer kubectl sur le Nœud Master** :
   ```bash
   mkdir -p $HOME/.kube
   sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
   sudo chown $(id -u):$(id -g) $HOME/.kube/config
   ```

3. **Installer le Plugin Réseau Calico** :
   ```bash
   kubectl apply -f https://docs.projectcalico.org/manifests/calico.yaml
   ```

4. **Ajouter les Nœuds Workers** :
   Préparation des nœuds workers (identique pour chaque worker) :
   ```bash
   sudo swapoff -a
   sudo sed -i '/ swap / s/^/#/' /etc/fstab
   cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
   net.ipv4.ip_forward = 1
   EOF
   sudo sysctl --system
   cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
   overlay
   br_netfilter
   EOF
   sudo modprobe overlay
   sudo modprobe br_netfilter
   sudo apt-get update
   sudo apt-get -y install ca-certificates curl
   sudo install -m 0755 -d /etc/apt/keyrings
   sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
   sudo chmod a+r /etc/apt/keyrings/docker.asc
   echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   sudo apt-get update
   sudo apt-get -y install containerd.io
   sudo kubeadm join 10.12.30.11:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
   ```

   Remplacez `<token>` et `<hash>` par les valeurs générées sur le nœud master.

5. **Vérification** :
   - Sur le nœud master :
     ```bash
     kubectl get nodes
     ```
   - Surveillez les pods du système Kubernetes :
     ```bash
     kubectl get pods -n kube-system
     ```

---

## Configuration de Base

### Prérequis
- **Nœud Master** :
  - kubeadm
  - kubelet
  - kubectl
  - Containerd (ou Docker si nécessaire).

- **Nœuds Workers** :
  - kubeadm
  - kubelet
  - Containerd (ou Docker si nécessaire).

- **Machine de Développement** :
  - Docker et docker-compose
  - .NET SDK 8 (LTS)
  - VS Code (nano).

### Réseau
- **Plugin réseau** : Calico
- **CIDR pour les Pods** : `192.168.0.0/16`

### Structure
1. **Cluster Kubernetes** :
   - 1 nœud master.
   - 2 nœuds workers.

2. **Services** :
   - API Backend (SUPMAP.API).
   - Interface Web et Mobile (à venir).

---

Pour toute question ou problème, contactez l'équipe projet.
