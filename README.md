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
#### Статьи по установке k8s: </br>
https://rebrainme.com/blog/kubernetes/sozdanie-klastera-kubernetes-na-vps-s-pomoshhyu-kubespray/ </br>
https://serveradmin.ru/kubernetes-ustanovka/ </br>
Статья по установке docker на Centos 8: </br>
https://phoenixnap.com/kb/how-to-install-docker-on-centos-8  </br>
Установка k8s + calico: </br>
https://www.kryukov.biz/kubernetes/ustanovka-klastera-kubernetes/ </br>
Статья по настройке ingress: </br>
https://serveradmin.ru/nastroyka-kubernetes/#_Ingress </br>

### Installation k8s on CentOS 8.4:  </br>
Установку производить с master ноды. </br>
На все ноды требуется установить пррограммы: </br> 
yum update -y </br>
yum install epel-release -y </br>
yum install wget -y </br>
yum install curl -y </br>
yum install git -y </br>
yum install python2 -y </br>
yum install python3 -y </br>
yum install sshpass -y </br>
yum install mc -y </br>
yum install ansible -y </br>
curl "https://bootstrap.pypa.io/get-pip.py" -o "get-pip.py" </br>
python3 get-pip.py </br>
pip install --ignore-installed requests==2.23.0 </br>
Установка docker на Centos:  </br>
dnf config-manager --add-repo=https://download.docker.com/linux/centos/docker-ce.repo  </br>
sudo dnf install docker-ce --nobest --allowerasing -y </br>

-------------------------------------------------------------------------------------------------
### Отключить firewall, swap, selinux: </br>
systemctl stop firewalld && systemctl disable firewalld && systemctl mask firewalld </br>
swapoff -a  </br>
vi /etc/fstab - требуется закоментировать строку - swap </br>
sudo setenforce 0  </br>
vi /etc/selinux/config - изменить SELINUX=permissive на SELINUX=disabled  </br>
reboot </br>

-------------------------------------------------------------------------------------------------- 
### Дальнейшие действия выполнять только на мастер ноде </br>
cd ~ </br>
git clone https://github.com/kubernetes-sigs/kubespray </br>
cd ~/kubespray </br>
pip install -r requirements.txt </br>

---------------------------------------------------------------------------------------------------
Копируем и изменяем файл инвентаря: </br>
cp -R ~/kubespray/inventory/sample ~/kubespray/inventory/dev </br>
Далее конфигурируем inventory.ini до требуемого состояния </br>
Мой файл [inventory.ini](https://github.com/murzinvit/12.04_kubernetes_install_part_2/blob/034cf987c022e050d9f093e0a4bf9848b7cfbf25/inventory/dev/inventory.ini) </br>

---------------------------------------------------------------------------------------------------
Сгенерить ssh ключи и разнести на все ноды в том числе и на localhost - master c которого все делаем </br>
ssh-keygen -t rsa </br>
cd ~/.ssh </br>
ssh-copy-id -i id_rsa.pub root@k8s-worker1 </br>
ssh-copy-id -i id_rsa.pub root@k8s-ingress1 </br>
и так далее на все ноды  </br>
Для мастер ноды: </br>
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys </br>
chmod og-wx ~/.ssh/authorized_keys  </br>

---------------------------------------------------------------------------------------------------
Установка кластера: </br>
cd ~/kubespray </br>
ansible-playbook -u root -i ./inventory/dev/inventory.ini cluster.yml -b --diff </br>

---------------------------------------------------------------------------------------------------
Возможно поребуется при установке: </br>
Установить ansible 3.4 на мастер ноду: `pip install --upgrade ansible==3` </br>
Установить python-netaddr:`yum install python-netaddr` </br>
Ести кватраты вместо русских букв: </br>
setfont UniCyr_8x16 </br>
Меняем в /etc/vconsole.conf FONT="UniCyr_8x16" </br>
Автодополнение в консоли для k8s: </br>
kubectl completion bash >> ~/.bashrc </br>
yum install bash-completion </br>

----------------------------------------------------------------------------------------------------
kubectl get nodes - если в выводе команды,в поле ROLES некоторые узлы помечены - none: </br>
kubectl label node k8s-worker1 node-role.kubernetes.io/worker=worker </br>
kubectl label node k8s-ingress1 node-role.kubernetes.io/ingress=ingress </br>
