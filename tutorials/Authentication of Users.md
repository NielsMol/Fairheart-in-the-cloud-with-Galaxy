## authentications of users:

this is enables users to create their account. also for yourself to acces your admin account (which you need to install tools)]
There by, in the config logs are there plenty of cool adjustments you can make https://github.com/galaxyproject/galaxy/blob/dev/lib/galaxy/config/sample/galaxy.yml.sample#1100

edit the /templates/ngninx/galaxy.j2 file
``nano /templates/ngninx/galaxy.j2``

edit the file that it looks like the following:

    @@ -14,6 +14,10 @@
             uwsgi_pass 127.0.0.1:8080;
             uwsgi_param UWSGI_SCHEME $scheme;
             include uwsgi_params;
    +        auth_basic           galaxy;
    +        auth_basic_user_file /etc/nginx/passwd;
    +        uwsgi_param          HTTP_REMOTE_USER $remote_user;
    +        uwsgi_param          HTTP_GX_SECRET SOME_SECRET_STRING;
         }

         # Static files can be more efficiently served by Nginx. Why send the
	
  
 Edit the groupvars/galaxyserver.yml:
``nano galaxyserver.yml``

edit the following:

    ...
    galaxy_config:
      galaxy:
        ...
        use_remote_user: true
        remote_user_maildomain: "{{ inventory_hostname }}"
        remote_user_secret: SOME_SECRET_STRING
	
#### sources
https://training.galaxyproject.org/training-material/topics/admin/tutorials/external-auth/tutorial.html
  
