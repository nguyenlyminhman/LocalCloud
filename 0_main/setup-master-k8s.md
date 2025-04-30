# 1. Setup docker 
### Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc


### Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | 
  \sudo tee /etc/apt/sources.list.d/docker.list > /dev/null 

sudo apt-get update


sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin


### Setup docker with current user
sudo usermod -aG docker ${USER}

su - ${USER}


# 2. Install K8S
sudo apt-get update
### apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl

### If the folder `/etc/apt/keyrings` does not exist, it should be created before the curl command, read the note below.
sudo mkdir -p -m 755 /etc/apt/keyrings

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

### This overwrites any existing configuration in /etc/apt/sources.list.d/kubernetes.list
echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update

sudo apt-get install -y kubelet kubeadm kubectl

sudo apt-mark hold kubelet kubeadm kubectl

### Check containerd running
systemctl status containerd

### Enable containerd or remove /etc/containerd/config.toml
change disabled_plugin to enabled_plugin
systemctl restart containerd

### Set IP Forward
sudo sysctl -w net.ipv4.ip_forward=1

# 3. Setup Master Node
### Add server IP to kube admin
sudo kubeadm init --kubernetes-version=v1.29.15
kubeadm config images pull
kubeadm init --apiserver-advertise-address=Your_Server_IP --pod-network-cidr=Your_Server_IP/16

### After adding server IP, Read carefully the instruction:
<!-- 
Your Kubernetes control-plane has initialized successfully! 

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root: 
-->

### Install calico network plugin
=> Access the calico home page


# 4. Setup Worker Node
Process the step 1 & 2.

From the master node, executing the print-join-command to get master token. Copy the information then executing in the worker node.

### Print join command from the master node.
kubeadm token create  print-join-command