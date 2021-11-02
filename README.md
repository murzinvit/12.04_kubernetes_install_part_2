### 12.04_kubernetes_install_part_2 </br>
#### Задание 1: Подготовить инвентарь kubespray: </br>
Новые тестовые кластеры требуют типичных простых настроек. Нужно подготовить инвентарь и проверить его работу. Требования к инвентарю:</br>
- подготовка работы кластера из 5 нод: 1 мастер и 4 рабочие ноды; </br>
- в качестве CRI — containerd; </br>
- запуск etcd производить на мастере.</br>

Установить ansible 3.4 на мастер ноду: `pip install --upgrade ansible==3` </br>
Установить python-netaddr:`yum install python-netaddr` </br>

### Рабочие записи: </br>

#### Installation k8s on CentOS 8.4
------------------------------
yum update -y </br>
yum install epel-release -y </br>
yum install wget -y </br>
yum install curl -y </br>
yum install git -y </br>
yum install python2 -y </br>
yum install python3 -y </br>
yum install sshpass -y </br>
----------------------------- </br>
curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py" </br>
python3 get-pip.py </br>
pip install --ignore-installed requests==2.23.0 </br>

---------------------------- </br>
cd ~ </br>
git clone https://github.com/kubernetes-sigs/kubespray </br>
cd ~/kubespray </br>
pip install -r requirements.txt </br>

------------------------- </br>
cd ~ </br>
cp -R ~/kubespray/inventory/sample ~/kubespray/inventory/dev </br>

-------------------------------------------------------------------------</br>
1. ssh-keygen -t rsa </br>
Press enter for each line  </br>
2. cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys </br>
3. chmod og-wx ~/.ssh/authorized_keys  </br>

--------------------------------------------------------------------------</br>
Установка docker на Centos:  </br>
https://phoenixnap.com/kb/how-to-install-docker-on-centos-8  </br>
dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo  </br>
sudo dnf install docker-ce --nobest --allowerasing  </br>

-------------------------------------------------------------------------- </br>
ansible-playbook -u root -i /root/kubespray/inventory/dev/inventory.ini cluster.yml -b --diff </br>
