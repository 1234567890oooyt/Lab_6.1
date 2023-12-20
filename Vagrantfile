Vagrant.configure("2") do |config|
    config.vm.box = "generic/ubuntu2204"
    config.ssh.forward_agent = false
    config.vm.synced_folder '.', '/vagrant', disabled: true

    #Створюємо 4 диски по 400мб
    config.vm.disk :disk, size: "400MB", name: "storage1"
    config.vm.disk :disk, size: "400MB", name: "storage2"
    config.vm.disk :disk, size: "400MB", name: "storage3"
    config.vm.disk :disk, size: "400MB", name: "storage4"

    config.vm.provider "virtualbox" do |vb|
      vb.name = "Lab6"
    end

    config.vm.provision "shell", inline: <<-SHELL
      apt-get -y install vim

	#Створюємо розділи за допомогою parted
      parted -a optimal --script /dev/sdb mklabel gpt mkpart primary ext4 0% 100% set 1 lvm on
      parted -a optimal --script /dev/sdc mklabel gpt mkpart primary ext4 0% 100% set 1 lvm on
      parted -a optimal --script /dev/sdd mklabel gpt mkpart primary ext4 0% 100% set 1 lvm on
      parted -a optimal --script /dev/sde mklabel gpt mkpart primary ext4 0% 100% set 1 lvm on

	#Створюємо фізичний том та групи з фізичних томів.
      pvcreate /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1
      vgcreate vg0 /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1

	#Створюємо логічниі томи по 800мб у группі.
      lvcreate -l 50%FREE vg0
      lvcreate -l 100%FREE vg0


      mkfs.ext4 /dev/vg0/lvol0
      mkfs.ext4 /dev/vg0/lvol1


      mkdir /mnt/vol{0,1}

      echo '/dev/vg0/lvol0 /mnt/vol0 ext4 defaults 0 0' >>/etc/fstab
      echo '/dev/vg0/lvol1 /mnt/vol1 ext4 defaults 0 0' >>/etc/fstab

      mount -a
    SHELL
  end