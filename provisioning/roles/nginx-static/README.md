Role Name
========

A simple role to serve static content with nginx. Only works on port 80 for now.

Requirements
------------

Requires someone to take care of the two following handlers :

    - restart nginx
    - reload nginx

Role Variables
--------------

nginx_static_sites:
    - name : 
      server_name: 
      alternate_server_name:
      root:

Example Playbook
-------------------------


    - hosts: servers
      roles:
        - role: nginx_static
          nginx_static_sites:
            - name: mysite
              server_name: mysite.com
              alternate_server_name: www.mysite.com
              root: /var/www

License
-------

Apache v2

Author Information
------------------

Pierre Paul Lefebvre <lefebvre@pheromone.ca>
