first-setup-centos7
===================

Ansible Playbook Example & Roles for CentOS 7 First Setup

Requirements
------------

**None**

Variables
---------

* TODO

Example
-------

    - hosts: all
      user: root
      pre_tasks:
        - name: be sure ssh key is added for selected users
          authorized_key:
            key: "{{ lookup('file', '~/.ssh/id_rsa.pub') }}"
            user: "{{item}}"
            state: present
          with_items:
            - root
            - USERNAME
      roles:
        - role: time
          time_use_chrony: yes
        - role: dracut
        - role: common
        - role: locale
        - role: cockpit
      post_tasks:
        - name: be sure to get the root logical volume
          shell: "mount | grep 'on / ' | awk '{print $1}'"
          register: root_lv
        - name: be sure a LVM snapshot volume is created
          command: 'lvcreate -l 20%ORIGIN -s --addtag snapshot -n snap_ansible_{{ansible_date_time.date}} {{ root_lv.stdout }}'
          when: "'/dev/' in root_lv.stdout"

License
-------

No License

Author Information
------------------

Djordje Stojanovic
