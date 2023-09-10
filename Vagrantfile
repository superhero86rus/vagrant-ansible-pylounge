# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|
  # Образ виртуальной машины с Vagrant Cloud
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

  # Правим /etc/sudoers чтобы не требовать пароль от УЗ ansible
  $script = <<-SCRIPT
    groupadd -f ansible
    useradd -m -p $(perl -e 'print crypt($ARGV[0], "password")' 'ansible') ansible -d /home/ansible -m -g ansible -s /bin/bash
    sudo chown -R ansible:ansible /home/ansible
    touch /etc/sudoers.d/ansible
    echo "ansible  ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/ansible
    sudo chown -R ansible:ansible /home/ansible
    mkdir -p "/home/ansible/.ssh"
    sudo chown -R ansible:ansible "/home/ansible/.ssh"
    cp -rT /etc/skel /home/ansible
  SCRIPT

  # Команда, которая вызовет на машине Shell инструкцию
  config.vm.define "app" do |app|
    # app.vm.provision "shell", inline: "uname -a"
    app.vm.provision "shell", inline: $script
  end

  # Команда, которая вызовет на машине Shell с инструкциями внешнего файла
  # config.vm.provision "shell", path: "provision.sh"

  # Копируем ключи на гостевую машину
  config.vm.provision "file", source: "/home/ansible/.ssh/id_rsa.pub", destination: "/home/ansible/.ssh/id_rsa.pub"

  # Команда, которая выполнится после создания машины и вызовет Ansible
  #config.vm.provision "ansible" do |ansible|
  #  ansible.playbook = "playbook.yml"
  #  ansible.become = true
  #  ansible.become_user = "root"
  #end
end