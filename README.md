### 12.04_kubernetes_install_part_2 </br>
#### Задание 1: Подготовить инвентарь kubespray: </br>
Новые тестовые кластеры требуют типичных простых настроек. Нужно подготовить инвентарь и проверить его работу. Требования к инвентарю:</br>
- подготовка работы кластера из 5 нод: 1 мастер и 4 рабочие ноды; </br>
- в качестве CRI — containerd; </br>
- запуск etcd производить на мастере.</br>
Результат: </br>
Команды для установки необходимых компонентов для развёртывания k8s через kubespray привёл в пункте "рабочии заметки" </br>
Файл [inventory.ini](https://github.com/murzinvit/12.04_kubernetes_install_part_2/blob/034cf987c022e050d9f093e0a4bf9848b7cfbf25/inventory/dev/inventory.ini) </br>
![screen](https://github.com/murzinvit/screen/blob/0bf2d23f165656e9e580234a0a617b470d17130f/Kuber_install_cluster_kubespray5.jpg) </br>


### Рабочие записи: </br>
Статьи по установке: </br>
https://rebrainme.com/blog/kubernetes/sozdanie-klastera-kubernetes-na-vps-s-pomoshhyu-kubespray/ </br>
https://serveradmin.ru/kubernetes-ustanovka/ </br>

#### Installation k8s on CentOS 8.4  </br>
Установка производилась с master ноды </br>
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
cp -R ~/kubespray/inventory/sample ~/kubespray/inventory/dev </br>
далее конфигурация inventory.ini </br>

-------------------------------------------------------------------------</br>
Сгенерить ssh ключи и разнести на все ноды в том числе и на localhost </br>
cd ~/.ssh </br>
ssh-keygen -t rsa </br>
ssh-copy-id -i id_rsa.pub root@remote_ip </br>
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys </br>
chmod og-wx ~/.ssh/authorized_keys  </br>

--------------------------------------------------------------------------</br>
Установка docker на Centos:  </br>
https://phoenixnap.com/kb/how-to-install-docker-on-centos-8  </br>
dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo  </br>
sudo dnf install docker-ce --nobest --allowerasing  </br>
docker run hello-world </br>
-------------------------------------------------------------------------- </br>
Установка кластера: </br>
ansible-playbook -u root -i /root/kubespray/inventory/dev/inventory.ini cluster.yml -b --diff </br>

-------------------------------------------------------------------------- </br>
Возможно поребуется при установке: </br>
Установить ansible 3.4 на мастер ноду: `pip install --upgrade ansible==3` </br>
Установить python-netaddr:`yum install python-netaddr` </br>
