##### Создать каркас Vagrantfile
```bash
vagrant init -m bento/ubuntu-20.04
```

##### Пример Vagrantfile для одной машины
```ruby
# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Образ виртуальной машины с Vagrant Cloud
  # В данном случае это локальный образ
  config.vm.box = "bento_ubuntu-20.04"
  
  # Настройки виртуальной машины и выбор провайдера
  config.vm.provider "virtualbox" do |vb|
    vb.name = "VagrantVM"
	# Отключаем интерфейс, он не понадобится
    vb.gui = false
	# 2 Гб оперативной памяти
    vb.memory = "2048"
	# Одноядерный процессор
    vb.cpus = 1
  end

  config.vm.hostname = "VagrantVM"

  config.vm.synced_folder ".", "/home/vagrant/code",
    owner: "www-data", group: "www-data"
  # Проброс портов 
  config.vm.network "forwarded_port", guest: 80, host: 8000
  config.vm.network "forwarded_port", guest: 3306, host: 33060
  # Команда для настройки сети
  config.vm.network "private_network", ip: "192.168.56.100"
  # Команда, которая выполнится после создания машины
  config.vm.provision "shell", path: "provision.sh"
end
```

##### Сохраняем образ локально и подключаем его к Vagrant
```bash
 vagrant box add --name bento_ubuntu-20.04 "~/Documents/images/vagrant/bento_ubuntu -20.04.box"
```

##### Установка имени хоста, чтобы понимать на какой мы машине (по SSH)
```bash
hostnamectl set-hostname node1.linux.local
```

##### Нужно обязательно добавить параметры SSH в hosts-файл
```ini
[webservers]
192.168.56.100 ansible_ssh_user=ansible ansible_ssh_pass=ansible
```

##### Самая простая проверка Ansible
```bash
ansible all -m ping -i ./ansible/hosts
```