
machines = {
  "rs1m1" => { "ip" => "200", "memory" => "1024", "cpus" => "1" },
  "rs1m2" => { "ip" => "201", "memory" => "1024", "cpus" => "1" },
  "rs1m3" => { "ip" => "202", "memory" => "1024", "cpus" => "1" },
}



$script = <<-SCRIPT
 cp /tmp/mongodb.repo /etc/yum.repos.d/ 
 dnf install -y mongodb-org
 systemctl enable mongod
 systemctl start mongod
SCRIPT



Vagrant.configure("2") do |config|

  config.vm.box = "generic/centos8"
  
  machines.each do |name,conf|
    config.vm.define "#{name}" do |machine|
      machine.vm.hostname = "#{name}.labmongodb.org"
      machine.vm.network "private_network", ip: "192.168.200.#{conf['ip']}"
      machine.vm.provider "virtualbox" do |vb|
        vb.memory = "#{conf["memory"]}"
        vb.cpus = "#{conf["cpus"]}"
        vb.gui = false
        vb.name = "#{name}"
      end
    end

  end

  # Copiando arquivos do Repositório
  config.vm.provision "file", source: "./mongodb.repo", destination:"/tmp/mongodb.repo"

  # Instalando MongoDB
  config.vm.provision "shell", inline: $script

  # Copiando arquivo de configuracao do MongoDB
  config.vm.provision "file", source: "./mongod.conf", destination:"/tmp/mongod.conf"

  # Configurando ReplicaSet e reiniciando
  config.vm.provision "shell", inline: "cp -f /tmp/mongod.conf /etc/mongod.conf && systemctl restart mongod"
  
end
