# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  # Configuración global del proveedor VirtualBox
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "1024"
    vb.cpus = 1
  end

  # =======================================================
  # 1. SERVIDOR UBUNTU - ROUTER Y SERVIDOR DHCP NATIVO
  # =======================================================
  config.vm.define "servidorUbuntu" do |servidor|
    servidor.vm.box = "ubuntu/jammy64"
    servidor.vm.hostname = "servidor-router"

    # Interfaz 1: NAT (Configurada por defecto por Vagrant para gestión SSH)

    # Interfaz 2: Red Interna A con IP estática en la LAN
    servidor.vm.network "private_network",
      ip: "192.168.40.2",
      netmask: "255.255.255.0",
      virtualbox__intnet: "internaAlumnado"
    
    # Interfaz 3: Red Interna S con IP estática en la LAN
    servidor.vm.network "private_network",
      ip: "192.168.50.2",
      netmask: "255.255.255.0",
      virtualbox__intnet: "internaSecretaria"
   
    # Interfaz 4: Red Interna P con IP estática en la LAN
    servidor.vm.network "private_network",
      ip: "192.168.60.2",
      netmask: "255.255.255.0",
      virtualbox__intnet: "internaProfesorado"


    # Aprovisionamiento mediante Ansible Local (se ejecuta dentro del servidor)
    servidor.vm.provision "ansible_local" do |ansible|
      ansible.playbook = "playbook.yml"
      ansible.install_mode = "pip"
      ansible.verbose = "v"
    end
  end

  # =======================================================
  # 2. CLIENTE LUBUNTU - RED INTERNA - ALUMNADO 40
  # =======================================================
  config.vm.define "clienteUbuntu" do |cliente|
    cliente.vm.box = "ubuntu/jammy64"
    cliente.vm.hostname = "clienteAlumnado"

    # Interfaz conectada a la red interna
    cliente.vm.network "private_network",
      ip: "192.168.40.10",
      netmask: "255.255.255.0",
      virtualbox__intnet: "internaAlumnado"
  end

  # =======================================================
  # 3. CLIENTE LUBUNTU - RED INTERNA - SECRETARIA 50
  # =======================================================
  config.vm.define "clienteUbuntu2" do |cliente|
    cliente.vm.box = "ubuntu/jammy64"
    cliente.vm.hostname = "clienteSecretaria"

    # Interfaz conectada a la red interna
    cliente.vm.network "private_network",
      ip: "192.168.50.10",
      netmask: "255.255.255.0",
      virtualbox__intnet: "internaSecretaria"
  end

  # =======================================================
  # 4. SERVIDOR WINDOWS - RED INTERNA - SERVIDOR DHCP - PROFESORADO 60
  # =======================================================
  config.vm.define "serverWindows" do |servidor|
    servidor.vm.box = "StefanScherer/windows_2019"
    
    #Indicar a Vagrant que el sistema invitado es Windows
    servidor.vm.guest = :windows
    
    #Configurar WinRM para la configuración en lugar de SSH
    servidor.vm.communicator = "winrm"
    servidor.vm.boot_timeout = 600
    
    servidor.vm.hostname = "serverProfesorado"

    config.vm.provider "virtualbox" do |vb|
      vb.memory = "2048"
      vb.cpus = 2
    end

    # Interfaz conectada a la red interna
    servidor.vm.network "private_network",
      type: "dhcp",
      mac: "08002729BB47",
      # ip: "192.168.60.10",
      # netmask: "255.255.255.0",
      virtualbox__intnet: "internaProfesorado"

    servidor.vm.provision "ansible" do |ansible|
      ansible.playbook = "playbook_w.yml"
      ansible.limit = "serverProfesorado"
      ansible.verbose = "v"
      ansible.extra_vars = {
        ansible_connection: "winrm",
        ansible_winrm_scheme: "http",
        ansible_winrm_transport: "basic",
        ansible_winrm_server_cert_validation: "ignore",
        ansible_user: "vagrant",
        ansible_password: "vagrant"
      }
    end
  end

  # =======================================================
  # 5. CLIENTE WINDOWS - RED INTERNA - PROFESORADO 60
  # =======================================================
  config.vm.define "clienteWindows" do |cliente|
    cliente.vm.box = "StefanScherer/windows_10"

    #Indicar a Vagrant que el sistema invitado es Windows
    cliente.vm.guest = :windows
    
    #Configurar WinRM para la configuración en lugar de SSH
    cliente.vm.communicator = "winrm"
    cliente.vm.boot_timeout = 600

    cliente.vm.hostname = "clienteProfesorado"

    # Interfaz conectada a la red interna
    cliente.vm.network "private_network",
      type: "dhcp",
      virtualbox__intnet: "internaProfesorado"
  end

end
