# Описываем Виртуальные машины
MACHINES = {
  # Указываем имя ВМ "ubuntu 22.04"
  :"UbuntuOtus2" => {
              # Какой vm box будем использовать
              :box_name => "ubuntu/focal64",
              # Указываем box_version
              :box_version => "20220427.0.0",
              # Указываем количество ядер ВМ
              :cpus => 2,
              # Указываем количество ОЗУ в мегабайтах
              :memory => 2048,
            }
}

Vagrant.configure("2") do |config|
  MACHINES.each do |boxname, boxconfig|
    # Отключаем проброс общей папки в ВМ
    config.vm.synced_folder ".", "/vagrant", disabled: true
    # Применяем конфигурацию ВМ
    config.vm.define boxname do |box|
      box.vm.box = boxconfig[:box_name]
      box.vm.box_version = boxconfig[:box_version]
      box.vm.host_name = boxname.to_s
      box.vm.provider "virtualbox" do |v|
        v.memory = boxconfig[:memory]
        v.cpus = boxconfig[:cpus]
		  #Установка и запуск Веб-сервера Apache2
  config.vm.provision "shell", inline: <<-SHELL
     sudo apt-get update
     sudo apt-get install -y apache2
	 sudo add-apt-repository ppa:cappelikan/ppa
	 sudo apt update && sudo apt full-upgrade
	 sudo apt install -y mainline
	 mainline check
	 mainline install-latest
	 reboot
  SHELL
      end
    end
  end
end
