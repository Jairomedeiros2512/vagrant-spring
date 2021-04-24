## sobre script
#instalando o mysqlserver versão 5.7 / criação de usuario, banco de dados para aplicação se conectar / duas ultimas linha é para o mysqle receber conexão fora da maquina  

$script_mysql = <<-SCRIPT
  apt-get update && \
  apt-get install -y mysql-server-5.7 && \
  mysql < /vagrant/mysql/script/user.sql && \
  mysql < /vagrant/mysql/script/schema.sql && \
  mysql < /vagrant/mysql/script/data.sql && \
  cat /vagrant/mysql/mysqld.cnf > /etc/mysql/mysql.conf.d/mysqld.cnf && \
  service mysql restart
SCRIPT

# Código Vagrant, deve instalar o Vagrant para interagir com Virtual Box
# Comando pode fazer pelo Power Shell pelo Windows
Vagrant.configure("2") do |config|  ## Declaração do Vagrant, informa que usa a versão 2 da linguagem e config é uma variavel
  config.vm.box = "ubuntu/bionic64" ## para gravar a maquina virtual dentro de um box como se fosse zipado

  config.vm.box_chek_update = false ## atualizar a versão box quando quando for atualizado na pagina do Vagrant

  config.vm.network "forwarded_port", guest: 80, host:3303 ## mapear uma porta na maquina virtual para uma porta no host em ambiente de fora da maquina virtual

  #config.vm.network "private_network", ip: "192.168.33.10" ///# a linha acima vai criar uma vpc podemos acessar por IP
  
  #config.vm.network "public_network"  /// rede publica onde conversa com todos que esta conectado no mesmo roteador, quem faz este processo é o Virtual Box
  
  #config.vm.synced_folder "../data", "/vagrant_data"  ///para mapear pasta para dentro do vagrant

  config.vm.provider "virtualbox" do |vb| ## configurar a maquina e interagir / definir o virtualizador
    vb.memory = 1024  # total de memoria
    vb.cpus = 1  # quantidade de nucleos
  end

  config.vm.define "mysqlserver" do |mysqlserver|  # criar uma maquina de mslqserver, 
    mysqlserver.vm.network "private_network", ip: "10.80.4.10"

    mysqlserver.vm.provider "virtualbox" do |vb|
      vb.name = "mysqlserver"
    end

    mysqlserver.vm.provision "shell", inline: $script_mysql # declarei que vai ser rodado um script
  end

  config.vm.define "springapp" do |springapp| # define que vai criar outra maquina então teremos duas maquinas
    springapp.vm.network "private_network", ip: "10.80.4.14" # informque que é uma rede privada
    springapp.vm.network "forwarded_port", guest: 3303, host: 3303 # ele mapeia a porta 3303 da virtual para porta 3303 na maquina host

    springapp.vm.provider "virtualbox" do |vb| # redefini a configuração da segunda maquina para não ficar igual a primeira
      vb.name = "springapp"
      vb.memory = 4096
      vb.cpus = 2
    end
    

    # abaixo estou definindo os comandos linha por linha
    springapp.vm.provision "shell", inline: "apt-get update && apt-get install -y openjdk-11-jre unzip"
    springapp.vm.provision "shell", inline: "unzip -o /vagrant/springapp/springapp.zip -d /srv"
    springapp.vm.provision "shell", inline: "mkdir -p /var/log/springapp"
    springapp.vm.provision "shell", inline: "cp /vagrant/springapp/springapp.service /etc/systemd/system/springapp.service"
    springapp.vm.provision "shell", inline: "sudo systemctl start springapp.service"
    springapp.vm.provision "shell", inline: "sudo systemctl enable springapp.service"
  end

  config.vm.provision "shell", inline: <<- SHELL  ## provision fala se quer disparar comando e criação
    apt-get update
    apt-get install -u apche2
  SHELL
end
