### 12.04_kubernetes_install_part_2 </br>
#### Задание 1: Подготовить инвентарь kubespray: </br>
Новые тестовые кластеры требуют типичных простых настроек. Нужно подготовить инвентарь и проверить его работу. Требования к инвентарю:</br>
- подготовка работы кластера из 5 нод: 1 мастер и 4 рабочие ноды; </br>
- в качестве CRI — containerd; </br>
- запуск etcd производить на мастере.</br>

Установить Centos 7 на ьастер и воркер </br>
Установить ansible 3.4 на мастер ноду: `pip install --upgrade ansible==1.9.2` </br>

### Рабочие записи: </br>
Установка Docker https://docs.docker.com/engine/install/debian/ </br>
Установка Kubernetes: https://habr.com/ru/post/462473/ </br>
Установка containerd https://github.com/containerd/containerd/releases </br>
Справка по containerd: https://habr.com/ru/post/568274/ </br>


-----------------------------------------------------------------------------</br>
apt update</br>
apt upgrade -y</br>
swapoff -a </br>
apt install sudo gnupg curl apt-transport-https ca-certificates aptitude libseccomp2 -y </br>
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg</br>
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list</br>
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -</br>

wget https://github.com/containerd/containerd/releases/download/v1.5.7/containerd-1.5.7-linux-amd64.tar.gz</br>
tar -xzf containerd-1.5.7-linux-amd64.tar.gz</br>
chmod -R 777 /opt/bin</br>
modprobe overlay</br>

echo "[Service]</br>
Environment="KUBELET_EXTRA_ARGS=--container-runtime=remote --runtime-request-timeout=15m --container-runtime-endpoint=unix:///run/containerd/containerd.sock"" ></br>
/etc/systemd/system/kubelet.service.d/0-containerd.conf</br>


apt-get install containerd -y</br>
