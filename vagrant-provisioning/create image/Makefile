box:
	-vagrant box remove -f init_kubenates --all --no-color
	-virsh vol-delete --pool default init_kubenates_vagrant_box_image_0.img
	sudo chmod +r /boot/vmlinuz-* # workaround for libguestfs bug, see https://askubuntu.com/questions/1046828/how-to-run-libguestfs-tools-tools-such-as-virt-make-fs-without-sudo
	export VAGRANT_VAGRANTFILE=/home/cbcadmin/kubernetes-cilium-vagrant/vagrant-provisioning/create image/Vagrantfile; \
	export VAGRANT_LIBVIRT_VIRT_SYSPREP_OPERATIONS="defaults,-ssh-userdir,-ssh-hostkeys,-lvm-uuids"; \
	export VAGRANT_LIBVIRT_VIRT_SYSPREP_OPTIONS="--delete /etc/machine-id --firstboot-command 'systemd-machine-id-setup; sleep 1; netplan apply'"; \
	vagrant up && \
	vagrant package --output init_kubenates.box && \
	vagrant destroy -f
	vagrant box add --name init_kubenates init_kubenates.box
	rm init_kubenates.box