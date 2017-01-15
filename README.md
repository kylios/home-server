Home Server
=====

This project contains Ansible scripts for setting up a home server. This server runs software such as:

* [Home Assistant](https://home-assistant.io)
* [Plex Media Server](https://www.plex.tv/)

# Setting up the build environment

I deploy to my home server using automated deployment scripts written in [Ansible](https://www.ansible.com/). I use [vagrant](https://www.vagrantup.com/) to manage a virtual environment that I can test any changes I make before I deploy them. We'll need to do just a few things to get all that setup.

First we need to install some dependencies:

- Install some packages from apt:
  ```bash
  $ sudo apt-get install libssl-dev sshpass
  ```
  - `libssl-dev`: this is needed to compile `cryptography`, which pip will try to do when installing our local python dependencies.
  - `sshpass`: this is needed to run deployment against a physical raspberry pi, which uses an SSH password.

- Create a virtual environment:
  ```bash
  $ virtualenv venv
  (venv) $
  ```

  Notice how your shell prompt changes. `virtualenv` modifies some environment variables and creates a local place for python packages to be installed. You'll need to make sure to activate the virtual environment every time you start work in a new shell.

  *Note* that if your shell prompt already has `(venv)` in it, you can skip this step since your virtual environment is active.

  ```bash
  $ . ./env/bin/activate
  (venv) $
  ```

- Install required python packages:
  ```bash
  (venv) $ pip install -r local-requirements.txt
  ```

Now to start up the development vm, we just start vagrant:

- Start the virtual machine:
  ```bash
  (venv) $ vagrant up
  ```

  The first time you run `vagrant up`, it will automatically provision the machine, which means invoking `ansible` to install python and Home Assistant. If you make changes to any of the `ansible` files and need to test them by applying them to the virtual machine, you'll need to re-provision the VM, like so:
  ```bash
  (venv) $ vagrant provision
  ```

# Setting up a Raspberry Pi

If you have a raspberry pi, you can use the same `ansible` scripts to deploy the home server applications to it. You'll need to ensure that the ip address, user, and password are correct for your pi. Open the file `ansible/environments/raspberry-pi/groups`. Mine looks like this:

```yaml
[home-assistant]
192.168.1.13 ansible_user=pi ansible_ssh_pass=raspberry
```

Update the ip address, `ansible_user`, and `ansible_ssh_pass` to match your own.

Now simply provision your machine by invoking `ansible-playbook`, like so:

```bash
(venv) $ ansible-playbook -i ansible/environments/raspberry-pi ansible/playbooks/deploy/server.yml
```

Once this is complete, you should have a raspberry pi now acting as a home server. Let's do some checks to verify everything is running ok.

- Check that it's running Home Assistant by visiting `<your-rpi-ip>:8123`.