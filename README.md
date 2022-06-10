* Laboratory work X
Данная лабораторная работа посвещена изучению процесса создания и конфигурирования виртуальной среды разработки с использованием Vagrant*
$ open https://www.vagrantup.com/intro/index.html

Tasks
	•	1. Ознакомиться со ссылками учебного материала
	•	2. Выполнить инструкцию учебного материала
	•	3. Составить отчет и отправить ссылку личным сообщением в Slack

Tutorial
$ export GITHUB_USERNAME=<имя_пользователя>
$ export PACKAGE_MANAGER=<пакетный_менеджер>
$ cd ${GITHUB_USERNAME}/workspace
$ ${PACKAGE_MANAGER} install vagrant
$ vagrant version
$ vagrant init bento/ubuntu-19.10	
Это инициализирует текущий каталог как среду Vagrant, создавая начальный файл Vagrant, если он еще не существует.

Если указан первый аргумент, он предварительно заполнит параметр config.vm.box в созданном Vagrantfile.

Если задан второй аргумент, он предварительно заполнит параметр config.vm.box_url в созданном Vagrantfile.
$ less Vagrantfile
$ vagrant init -f -m bento/ubuntu-19.10
--force — если указано, эта команда перезапишет любой существующий Vagrantfile.

--minimal — если указано, будет создан минимальный Vagrantfile. Этот Vagrantfile не содержит комментариев с инструкциями, которые содержит обычный Vagrantfile.
$ mkdir shared
$ cat > Vagrantfile <<EOF
\$script = <<-SCRIPT
sudo apt install docker.io -y
sudo docker pull fastide/ubuntu:19.04
sudo docker create -ti --name fastide fastide/ubuntu:19.04 bash
sudo docker cp fastide:/home/developer /home/
sudo useradd developer
sudo usermod -aG sudo developer
echo "developer:developer" | sudo chpasswd
sudo chown -R developer /home/developer
SCRIPT
EOF
$ cat >> Vagrantfile <<EOF

Vagrant.configure("2") do |config|

  config.vagrant.plugins = ["vagrant-vbguest"]
EOF
$ cat >> Vagrantfile <<EOF

  config.vm.box = "bento/ubuntu-19.10"
  config.vm.network "public_network"
  config.vm.synced_folder('shared', '/vagrant', type: 'rsync')

  config.vm.provider "virtualbox" do |vb|
    vb.gui = true
    vb.memory = "2048"
  end

  config.vm.provision "shell", inline: \$script, privileged: true

  config.ssh.extra_args = "-tt"

end
EOF
$ vagrant validate
Ратификация файла
$ vagrant status
состоянии машин, которыми управляет Vagrant.
$ vagrant up # --provider virtualbox
Эта команда создает и настраивает гостевые машины в соответствии с вашим Vagrantfile.

Это самая важная команда в Vagrant, поскольку именно так создается любая машина Vagrant. Любой, кто использует Vagrant, должен ежедневно использовать эту команду.
$ vagrant port
The port command displays the full list of guest ports mapped to the host machine ports:
$ vagrant status
$ vagrant ssh
Это подключит SSH к работающей машине Vagrant и даст вам доступ к оболочке.

В простом бродячем проекте созданный экземпляр будет называться по умолчанию.

Vagrant подключится к этому экземпляру по ssh без имени экземпляра:
$ vagrant snapshot list
Моментальные снимки записывают состояние гостевой машины на определенный момент времени. Затем вы можете быстро восстановить эту среду. Это позволяет вам экспериментировать и пробовать разные вещи, а также быстро возвращаться к предыдущему состоянию.
Эта команда выведет список всех сделанных снимков.
$ vagrant snapshot push
This takes a snapshot and pushes it onto the snapshot stack.
This is a shorthand for vagrant snapshot save where you do not need to specify a name. When you call the inverse vagrant snapshot pop, it will restore the pushed state.
$ vagrant snapshot list
$ vagrant halt
Эта команда выключает работающую машину, которой управляет Vagrant.

Сначала Vagrant попытается корректно выключить машину, запустив механизм выключения гостевой ОС. Если это не удается или если указан флаг --force, Vagrant фактически просто отключит питание машины.
$ vagrant snapshot pop
Эта команда обратна vagrant snapshot push: она восстанавливает отправленное состояние.
  config.vm.provider :vmware_esxi do |esxi|

    esxi.esxi_hostname = '<exsi_hostname>'
    esxi.esxi_username = 'root'
    esxi.esxi_password = 'prompt:'

    esxi.esxi_hostport = 22

    esxi.guest_name = '${GITHUB_USERNAME}'

    esxi.guest_username = 'vagrant'
    esxi.guest_memsize = '2048'
    esxi.guest_numvcpus = '2'
    esxi.guest_disk_type = 'thin'
  end
$ vagrant plugin install vagrant-vmware-esxi
Это устанавливает плагин с заданным именем или путем к файлу. Если имя не является путем к файлу, то плагин устанавливается из удаленных репозиториев, обычно RubyGems. Эта команда также обновит плагин, если он уже установлен, но для этого вы также можете использовать бродячее обновление плагина.
$ vagrant plugin list
$ vagrant up --provider=vmware_esxi

Report
$ cd ~/workspace/
$ export LAB_NUMBER=10
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER}.git tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
