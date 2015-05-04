# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.require_version ">= 1.7.2"

Vagrant.configure(2) do |config|

  config.ssh.insert_key = true

  config.vm.box = "ubuntu/trusty64"
  config.vm.box_check_update = false
  config.vm.define "ansible_flask"

  config.vm.hostname = 'ansible-flask.local'
  config.vm.network "private_network", ip: "192.168.60.60"
  config.vm.network "forwarded_port", guest: 80, host: 50060, auto_correct: true

  config.vm.provision :ansible do |ansible|
    ansible.playbook = "provisioning/ansible/deploy.yml"
    ansible.extra_vars = {
      nginx: {
        sites_conf: [
          {
            src_path: ENV['FLASK_APPLICATION_PATH'] + '/provisioning/roles/deployer/templates/flask_skeleton.nginx.conf.j2',
            target_name: 'flask_skeleton.conf'
          }
        ]
      },
      uwsgi: {
        apps_conf: [
          {
            src_path: ENV['FLASK_APPLICATION_PATH'] + '/provisioning/roles/deployer/templates/flask_skeleton.uwsgi.ini.j2',
            target_name: 'flask_skeleton.ini'
          }
        ]
      },
      supervisor: {
        conf: {
          path: 'supervisord.conf.j2'
        },
        apps_conf: [
          {
            src_path: ENV['FLASK_APPLICATION_PATH'] + '/provisioning/roles/deployer/templates/flask_skeleton.supervisor.conf.j2',
            target_name: 'flask_skeleton.conf'
          }
        ]
      },
      flask_application: {
        user: 'www-data',
        group: 'www-data',
        src: {
          path: ENV['FLASK_APPLICATION_PATH'] + '/flask_skeleton',
          requirements_path: ENV['FLASK_APPLICATION_PATH'] + '/requirements.txt'
        },
        target: {
          path: '/var/www/flask_applications/flask_skeleton/app',
          venvs_path: '/var/www/flask_applications/flask_skeleton/venvs',
          static_path: '/var/www/flask_applications/flask_skeleton/app/current/flask_skeleton/apps/static'
        },
        dependencies: [
          { package: 'python2.7', version: '2.7.6-8' },
          { package: 'python-pip', version: '1.5.4-1ubuntu1' },
          { package: 'python-virtualenv', version: '1.11.4-1' },
          { package: 'python-dev', version: '2.7.5-5ubuntu3' },
        ]
      }
    }
    ansible.inventory_path = "provisioning/ansible/ansible_hosts"
    # ansible.verbose = 'vvvv'
    ansible.limit = 'web'
  end

  config.vm.provider "virtualbox" do |vm|
    vm.name = "ansible_flask"
  end
end
