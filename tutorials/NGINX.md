### NGINX

note\
after installing galaxy isnt acfesble anymore by localhost:8080.\
we figured out that after the part authentication. you can just use the website name: and the the login and password

``nano galaxy.yml``
add the following role:
``galaxyprojext.nginx``

``nano group_vars/galaxyserver.yml``
update the http from 0.0.0.0:8080 to 127.0.0.1:8080

    -    http: 0.0.0.0:8080
    +    socket: 127.0.0.1:8080

``nano /groupvars/galaxyserver.yml``

Add the following to galaxyserver.yml

    # Certbot
    certbot_auto_renew_hour: "{{ 23 |random(seed=inventory_hostname)  }}"
    certbot_auto_renew_minute: "{{ 59 |random(seed=inventory_hostname)  }}"
    certbot_auth_method: --webroot
    certbot_install_method: virtualenv
    certbot_auto_renew: yes
    certbot_auto_renew_user: root
    certbot_environment: production 							# was staging before but production server
    certbot_well_known_root: /srv/nginx/_well-known_root
    certbot_share_key_users:
      - nginx
    certbot_post_renewal: |
        systemctl restart nginx || true
    certbot_domains:
    - "{{ inventory_hostname }}"
    certbot_agree_tos: --agree-tos
    
    # NGINX
    nginx_selinux_allow_local_connections: true
    nginx_servers:
    - redirect-ssl
    nginx_enable_default_server: false
    nginx_ssl_servers:
    - galaxy
    nginx_conf_http:
    client_max_body_size: 5g # was 1 gb before but this allows to upload big data of patients until 5 gb
    nginx_ssl_role: usegalaxy_eu.certbot
    nginx_conf_ssl_certificate: /etc/ssl/certs/fullchain.pem
    nginx_conf_ssl_certificate_key: /etc/ssl/user/privkey-nginx.pem


preform the following commands:\
``cd galaxyproject.nginx/templates``\
``mkdir nginx``\
``nano redirect-ssl.j2``

add the following:
        
        server {
        listen 80 default_server;
        listen [::]:80 default_server;

        server_name "{{ inventory_hostname }}";

       location /.well-known/ {
            root {{ certbot_well_known_root }};
        }

     location / {
            return 302 https://$host$request_uri;
        }
    }

``nano galaxy.j2``

add the following:

    server {
        # Listen on port 443
        listen        *:443 ssl default_server;
        # The virtualhost is our domain name
        server_name   "{{ inventory_hostname }}";

        # Our log files will go here.
        access_log  /var/log/nginx/access.log;
        error_log   /var/log/nginx/error.log;

        # The most important location block, by default all requests are sent to uWSGI
        location / {
            # This is the backend to send the requests to.
            uwsgi_pass 127.0.0.1:8080;
            uwsgi_param UWSGI_SCHEME $scheme;
            include uwsgi_params;
        }

         # Static files can be more efficiently served by Nginx. Why send the
         # request to uWSGI which should be spending its time doing more useful
         # things like serving Galaxy!
        location /static {
            alias {{ galaxy_server_dir }}/static;
            expires 24h;
        }

       # The style directory is in a slightly different location
        location /static/style {
            alias {{ galaxy_server_dir }}/static/style/blue;
            expires 24h;
        }

        # In Galaxy instances started with run.sh, many config files are
        # automatically copied around. The welcome page is one of them. In
        # production, this step is skipped, so we will manually alias that.
        location /static/welcome.html {
            alias {{ galaxy_server_dir }}/static/welcome.html.sample;
            expires 24h;
        }

        # serve visualization and interactive environment plugin static content
        location ~ ^/plugins/(?<plug_type>[^/]+?)/((?<vis_d>[^/_]*)_?)?(?<vis_name>[^/]*?)/static/(?<static_file>.*?)$ {
            alias {{ galaxy_server_dir }}/config/plugins/$plug_type/;
            try_files $vis_d/${vis_d}_${vis_name}/static/$static_file
                      $vis_d/static/$static_file =404;
        }

        location /robots.txt {
            alias {{ galaxy_server_dir }}/static/robots.txt;
        }

        location /favicon.ico {
            alias {{ galaxy_server_dir }}/static/favicon.ico;
        }
    }


#### change the ip to a domain name

``nano hosts``\
change ip adress to website name, http://fairheartgalaxy.bioinformatics-atgm.nl


open the ports\
``Sudo ufw allow 443``\
``sudo ufw allow 80``

play the playbook\
``sudo ansible-playbook galaxy.yml``

you should see something like this:

RUNNING HANDLER [restart galaxy] ****************************************\
changed: [galaxy.example.org]



##### sources
https://docs.galaxyproject.org/en/master/admin/nginx.html\


