# -*- mode: ruby -*-
# vi: set ft=ruby :

require 'yaml'

raise Vagrant::Errors::VagrantError.new,
  "'settings.yml' file not found.\n\n" \
  "Please define your 'settings.yml'. " \
  "See 'settings.sample.yml' for an example." unless File.exist?("settings.yml")

settings = YAML.load_file('settings.yml')

num_volumes = settings.has_key?('vm_num_volumes') ?
              settings['vm_num_volumes'] : 2

volume_size = settings.has_key?('vm_volume_size') ?
              settings['vm_volume_size'] : '8G'

num_nodes = settings.has_key?('num_nodes') ?
            settings['num_nodes'] : 3

raise Vagrant::Errors::VagrantError.new,
  "Invalid 'num_nodes' (#{num_nodes}).\n\n" \
  "Supported values: 2 or 3" unless ([2,3]).include?(num_nodes)

Vagrant.configure("2") do |config|
  config.ssh.insert_key = false

  config.vm.box = "suse/SLE12SP3-x86_64"

  config.vm.provider "libvirt" do |lv|
    if settings.has_key?('libvirt_host') then
      lv.host = settings['libvirt_host']
    end
    if settings.has_key?('libvirt_user') then
      lv.username = settings['libvirt_user']
    end
    if settings.has_key?('libvirt_use_ssl') then
      lv.connect_via_ssh = true
    end

    lv.memory = settings.has_key?('vm_memory') ? settings['vm_memory'] : 4096
    lv.cpus = settings.has_key?('vm_cpus') ? settings['vm_cpus'] : 2
    if settings.has_key?('vm_storage_pool') then
      lv.storage_pool_name = settings['vm_storage_pool']
    end
  end

  config.vm.define :node1 do |node|
    node.vm.hostname = "node1.oa.local"
    node.vm.network :private_network, ip: "192.168.100.201"
    node.vm.network :private_network, ip: "192.168.170.201"

    node.vm.provision "file", source: "keys/id_rsa",
                              destination:".ssh/id_rsa"
    node.vm.provision "file", source: "keys/id_rsa.pub",
                              destination:".ssh/id_rsa.pub"

    node.vm.synced_folder ".", "/vagrant", disabled: true

    node.vm.provider "libvirt" do |lv|
      (1..num_volumes).each do |d|
        lv.storage :file, size: volume_size, type: 'qcow2'
      end
    end

    node.vm.provision "shell", inline: <<-SHELL
      echo "192.168.100.200 salt salt.oa.local" >> /etc/hosts
      echo "192.168.100.201 node1 node1.oa.local" >> /etc/hosts
      echo "192.168.100.202 node2 node2.oa.local" >> /etc/hosts
      echo "192.168.100.203 node3 node3.oa.local" >> /etc/hosts
      cat /home/vagrant/.ssh/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys
      chmod 600 /home/vagrant/.ssh/id_rsa
      cp /home/vagrant/.ssh/id_rsa* /root/.ssh/
      cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
      hostname node1

      SuSEfirewall2 off

      zypper -n install ntp
      zypper -n install salt-minion
      systemctl enable salt-minion
      systemctl start salt-minion

      touch /tmp/ready
    SHELL
  end

  if num_nodes > 1 then
    config.vm.define :node2 do |node|
        node.vm.hostname = "node2.oa.local"
        node.vm.network :private_network, ip: "192.168.100.202"
        node.vm.network :private_network, ip: "192.168.170.202"

        node.vm.provision "file", source: "keys/id_rsa",
                                destination:".ssh/id_rsa"
        node.vm.provision "file", source: "keys/id_rsa.pub",
                                destination:".ssh/id_rsa.pub"

        node.vm.synced_folder ".", "/vagrant", disabled: true

        node.vm.provider "libvirt" do |lv|
          (1..num_volumes).each do |d|
            lv.storage :file, size: volume_size, type: 'qcow2'
          end
        end

        node.vm.provision "shell", inline: <<-SHELL
        echo "192.168.100.200 salt salt.oa.local" >> /etc/hosts
        echo "192.168.100.201 node1 node1.oa.local" >> /etc/hosts
        echo "192.168.100.202 node2 node2.oa.local" >> /etc/hosts
        echo "192.168.100.203 node3 node3.oa.local" >> /etc/hosts
        cat /home/vagrant/.ssh/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys
        chmod 600 /home/vagrant/.ssh/id_rsa
        cp /home/vagrant/.ssh/id_rsa* /root/.ssh/
        cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
        hostname node2

        ssh-keyscan -H salt >> ~/.ssh/known_hosts
        ssh-keyscan -H node1 >> ~/.ssh/known_hosts
        ssh-keyscan -H node3 >> ~/.ssh/known_hosts

        SuSEfirewall2 off

        zypper -n install ntp
        zypper -n install salt-minion
        systemctl enable salt-minion
        systemctl start salt-minion

        touch /tmp/ready
        SHELL
    end
  end
  
  if num_nodes > 2 then
    config.vm.define :node3 do |node|
        node.vm.hostname = "node3.oa.local"
        node.vm.network :private_network, ip: "192.168.100.203"
        node.vm.network :private_network, ip: "192.168.170.203"

        node.vm.provision "file", source: "keys/id_rsa",
                                destination:".ssh/id_rsa"
        node.vm.provision "file", source: "keys/id_rsa.pub",
                                destination:".ssh/id_rsa.pub"

        node.vm.synced_folder ".", "/vagrant", disabled: true

        node.vm.provider "libvirt" do |lv|
          (1..num_volumes).each do |d|
            lv.storage :file, size: volume_size, type: 'qcow2'
          end
        end

        node.vm.provision "shell", inline: <<-SHELL
        echo "192.168.100.200 salt salt.oa.local" >> /etc/hosts
        echo "192.168.100.201 node1 node1.oa.local" >> /etc/hosts
        echo "192.168.100.202 node2 node2.oa.local" >> /etc/hosts
        echo "192.168.100.203 node3 node3.oa.local" >> /etc/hosts
        cat /home/vagrant/.ssh/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys
        chmod 600 /home/vagrant/.ssh/id_rsa
        cp /home/vagrant/.ssh/id_rsa* /root/.ssh/
        cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys

        ssh-keyscan -H salt >> ~/.ssh/known_hosts
        ssh-keyscan -H node1 >> ~/.ssh/known_hosts
        ssh-keyscan -H node2 >> ~/.ssh/known_hosts

        hostname node3

        SuSEfirewall2 off

        zypper -n install ntp
        zypper -n install salt-minion
        systemctl enable salt-minion
        systemctl start salt-minion

        touch /tmp/ready
        SHELL
    end
  end

  config.vm.define :salt do |salt|
    salt.vm.hostname = "salt.oa.local"
    salt.vm.network :private_network, ip: "192.168.100.200"

    salt.vm.provision "file", source: "keys/id_rsa",
                              destination:".ssh/id_rsa"
    salt.vm.provision "file", source: "keys/id_rsa.pub",
                              destination:".ssh/id_rsa.pub"

    salt.vm.provision "file", source: "bin",
                              destination:"."

    roles = {
        "mon" => "node*",
        "igw" => ("node[12]*" if num_nodes > 1) || "node*",
        "rgw" => ("node[13]*" if num_nodes > 2) || "node*",
        "mds" => ("node[23]*" if num_nodes > 2) || "node*",
        "ganesha" => ("node[23]*" if num_nodes > 2) || "node*",
        "mgr" => ("node[12]*" if num_nodes > 1) || "node*"
    }

    salt.vm.provision "shell", inline: <<-SHELL
      echo "192.168.100.200 salt salt.oa.local" >> /etc/hosts
      echo "192.168.100.201 node1 node1.oa.local" >> /etc/hosts
      echo "192.168.100.202 node2 node2.oa.local" >> /etc/hosts
      echo "192.168.100.203 node3 node3.oa.local" >> /etc/hosts
      cat /home/vagrant/.ssh/id_rsa.pub >> /home/vagrant/.ssh/authorized_keys
      chmod 600 /home/vagrant/.ssh/id_rsa
      cp /home/vagrant/.ssh/id_rsa* /root/.ssh/
      cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
      hostname salt

      zypper -n install ntp salt-minion salt-master git-core
      systemctl enable ntpd
      systemctl start ntpd

      systemctl enable salt-master
      systemctl start salt-master
      sleep 5
      systemctl enable salt-minion
      systemctl start salt-minion

      SuSEfirewall2 off

      while : ; do
        PROVISIONED_NODES=`ls -l /tmp/ready-* 2>/dev/null | wc -l`
        echo "waiting for nodes (${PROVISIONED_NODES}/#{num_nodes})";
        [[ "${PROVISIONED_NODES}" != "#{num_nodes}" ]] || break
        sleep 10;
        sudo scp -o StrictHostKeyChecking=no node1:/tmp/ready /tmp/ready-node1 2>/dev/null;
        sudo scp -o StrictHostKeyChecking=no node2:/tmp/ready /tmp/ready-node2 2>/dev/null;
        sudo scp -o StrictHostKeyChecking=no node3:/tmp/ready /tmp/ready-node3 2>/dev/null;
      done

      zypper ref
      zypper in -y deepsea   

        cat > /srv/salt/ceph/updates/default_my.sls <<EOF
dummy command:
  test.nop
EOF
        cp /srv/salt/ceph/updates/default_my.sls /srv/salt/ceph/updates/restart
        sed -i 's/default/default_my/g' /srv/salt/ceph/updates/init.sls
        sed -i 's/default/default_my/g' /srv/salt/ceph/updates/restart/init.sls
        cp /srv/salt/ceph/updates/default_my.sls /srv/salt/ceph/time
        sed -i 's/default/default_my/g' /srv/salt/ceph/time/init.sls

	echo "deepsea_minions: '*'" > /srv/pillar/ceph/deepsea_minions.sls
        
        sed -i 's/#worker_threads: 5/worker_threads: 10/g' /etc/salt/master
        chown -R salt:salt /srv/pillar
        systemctl restart salt-master
        sleep 10
        ssh -o StrictHostKeyChecking=no node1 "systemctl restart salt-minion.service"
        ssh -o StrictHostKeyChecking=no node2 "systemctl restart salt-minion.service"
        ssh -o StrictHostKeyChecking=no node3 "systemctl restart salt-minion.service"
        ssh -o StrictHostKeyChecking=no salt "systemctl restart salt-minion.service"
      
        sleep 5
        echo -e "Accept the salt-minion keys"
        salt-key -Ay
        
        echo "[DeepSea] Stage 0 - prep"
        salt-run state.orch ceph.stage.prep

        sleep 10
        echo "[DeepSea] Installing and Activating Salt-API"
        salt-call state.apply ceph.salt-api

        sleep 10
        echo "[DeepSea] Stage 1 - discovery"
        salt-run state.orch ceph.stage.discovery
        cat > /srv/pillar/ceph/proposals/policy.cfg <<EOF
# Cluster assignment
cluster-ceph/cluster/*.sls
# Hardware Profile
profile-default/cluster/*.sls
profile-default/stack/default/ceph/minions/*yml
# Common configuration
config/stack/default/global.yml
config/stack/default/ceph/cluster.yml
# Role assignment
role-master/cluster/salt*.sls
role-admin/cluster/salt*.sls
role-mon/cluster/#{roles['mon']}.sls
role-igw/cluster/#{roles['igw']}.sls
role-rgw/cluster/#{roles['rgw']}.sls
role-mds/cluster/#{roles['mds']}.sls
role-mon/stack/default/ceph/minions/#{roles['mon']}.yml
role-ganesha/cluster/#{roles['ganesha']}.sls
role-mgr/cluster/#{roles['mgr']}.sls
role-openattic/cluster/salt*.sls
EOF
        chown salt:salt /srv/pillar/ceph/proposals/policy.cfg
        cat > /srv/pillar/ceph/rgw.sls <<EOF
rgw_configurations:
  rgw:
    users:
      - { uid: "admin", name: "Admin", email: "admin@demo.nil", system: True }
EOF
        chown salt:salt /srv/pillar/ceph/rgw.sls

        sleep 2
        echo "[DeepSea] Stage 2 - configure"
        salt-run state.orch ceph.stage.configure
        sed -i 's/time_init:.*ntp/time_init: default_my/g' /srv/pillar/ceph/stack/default/global.yml

        sleep 5
        echo "[DeepSea] Stage 3 - deploy"
        DEV_ENV='true' salt-run state.orch ceph.stage.deploy

        sleep 5
        echo "[DeepSea] Stage 4 - services"
        DEV_ENV='true' salt-run state.orch ceph.stage.4

        chmod 644 /etc/ceph/ceph.client.admin.keyring

        touch /tmp/ready
    SHELL
  end
end
