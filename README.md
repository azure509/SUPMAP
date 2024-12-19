# SUPMAP
Contexte du projet :
La société fictive Trafine souhaite développer une application de navigation en temps réel, similaire à Waze, qui permettrait aux utilisateurs de recevoir des informations sur la circulation, les accidents et les obstacles.

Cette application devra également permettre aux utilisateurs de signaler des incidents et de recevoir des itinéraires optimisés en fonction des conditions de circulation. 

Le projet devra inclure une application mobile pour les utilisateurs, une interface web pour la gestion et la visualisation des données en temps réel, ainsi qu'une architecture de micro-services pour gérer les différentes fonctionnalités. 

Pour la bêta, Trafine souhaite se concentrer sur les trajets en France métropolitaine. 

 
# SUPMAP - Documentation

## Commandes pour Lancer l'Environnement

### Étape 1 : Initialisation du Cluster Kubernetes
1. **Configurer les Paramètres Réseau**
   Activez la transmission des paquets IPv4 :
   ```bash
   # Configurer les paramètres sysctl pour activer la transmission IPv4
   cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
   net.ipv4.ip_forward = 1
   EOF
   # Appliquer les modifications
   sudo sysctl --system
   ```

   Vérifiez que la transmission IPv4 est activée :
   ```bash
   sudo sysctl net.ipv4.ip_forward
   ```

2. **Charger les Modules du Noyau**
   Spécifiez et chargez les dépendances nécessaires :
   ```bash
   cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
   overlay
   br_netfilter
   EOF
   sudo modprobe overlay
   sudo modprobe br_netfilter
   ```

3. **Installer Containerd**
   - Ajouter la clé GPG officielle de Docker et le dépôt :
     ```bash
     sudo apt-get update
     sudo apt-get -y install ca-certificates curl
     sudo install -m 0755 -d /etc/apt/keyrings
     sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
     sudo chmod a+r /etc/apt/keyrings/docker.asc
     echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
     ```
   - Installer containerd :
     ```bash
     sudo apt-get update
     sudo apt-get -y install containerd.io
     ```
   - Configurer le pilote cgroup systemd pour containerd :
     ```bash
     sudo mkdir -p /etc/containerd
     sudo containerd config default | sudo tee /etc/containerd/config.toml
     sudo sed -i "s/SystemdCgroup = false/SystemdCgroup = true/g" "/etc/containerd/config.toml"
     sudo systemctl restart containerd
     sudo systemctl status containerd
     ```

4. **Installer Kubernetes (kubeadm, kubelet, kubectl)**
   - Installer les dépendances et la clé GPG de Kubernetes :
     ```bash
     sudo apt-get update
     sudo apt-get install -y apt-transport-https ca-certificates curl gpg
     curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
     echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list
     ```
   - Installer les outils Kubernetes :
     ```bash
     sudo apt-get update
     sudo apt-get install -y kubelet kubeadm kubectl
     sudo apt-mark hold kubelet kubeadm kubectl
     ```
   - (Facultatif) Activer kubelet :
     ```bash
     sudo systemctl enable --now kubelet
     ```

5. **Initialiser le Cluster Kubernetes**
   - Initialisez le plan de contrôle sur le nœud master :
     ```bash
     sudo kubeadm init --pod-network-cidr=192.168.0.0/16
     ```
   - Configurez kubectl pour accéder au cluster :
     ```bash
     mkdir -p $HOME/.kube
     sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
     sudo chown $(id -u):$(id -g) $HOME/.kube/config
     ```
   - Vérifiez l'état du nœud :
     ```bash
     kubectl get nodes
     ```

6. **Configurer le Réseau des Pods**
   - Installez Calico comme réseau CNI :
     ```bash
     kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/tigera-operator.yaml
     kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.28.0/manifests/custom-resources.yaml
     ```
   - Vérifiez les pods Calico :
     ```bash
     kubectl get pods -n calico-system
     ```

7. **Joindre les Nœuds Workers**
   - Exécutez cette commande sur chaque nœud worker pour les joindre au cluster (commande générée lors de l'initialisation) :
     ```bash
     sudo kubeadm join <control-plane-ip>:<control-plane-port> --token <token> --discovery-token-ca-cert-hash <hash>
     ```

8. **Ajouter un Rôle de Travailleur**
   - Attribuez un rôle de travailleur à un nœud worker :
     ```bash
     kubectl label node <node-name> node-role.kubernetes.io/worker=worker
     ```

9. **Vérifier l'État du Cluster**
   - Sur le nœud master :
     ```bash
     kubectl get nodes
     ```
   - Vérifiez que tous les pods du cluster sont en état de fonctionnement :
     ```bash
     kubectl get pods -A
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
  - VS Code (ou tout autre éditeur).

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

