# docker_deploy

## example configuration

```yaml
  #--------------------
  # EXAMPLE APP WITH ALL FEATURES
  #--------------------
  appname_docker_apps:
    - name: app_name
    
      externally_changed: "{{ conf_changed }}" 
                             #externally_changed forces a restart of the app
                             #this is usually called from another role for "inter role comm"
    
      app_version: 'v1'      #only use app_version OR app_env, NEVER both
                             #bare app_version is used if environments NOT desired
    
      app_env:
        integ:
          app_version: 'v1'
        staging:
          app_version: 'v2'
        prod:
          app_version: 'v3'
        prod_dc2:
          app_version: 'v4'
          image: 'hub.example.com/app_name2' #OPTIONAL, overrides the default image name below
        prod_disabled:
          ## set both to disabled to disable this container
          ## for this specific environment
          app_version: 'disabled'
          image:       'disabled'  

      pull_containers: True    #OPTIONAL: set to false to NOT pull containers
                               # this assumes that you've manually pulled the images
      docker:
        image: 'hub.example.com/app_name'
        state: present         #OPTIONAL
                               #DEFAULT: started
                               #CHOICES: [ absent, present, reloaded, started ]
        restart_policy: 'no'   #OPTIONAL
                               #recommended to set to 'no' if state: present
                               #CHOICES: [ "no", "on-failure", "always", "unless-stopped" ]
        command: 'run'         #OPTIONAL
        log_driver: 'syslog'   #OPTIONAL
        log_options:           #OPTIONAL
          tag: app_name        
        expose:                #OPTIONAL
          - 8080
        ports:                 #OPTIONAL
          - '8080:8080'
        volumes:               #OPTIONAL
        env:                   #OPTIONAL environment variables to inject
          PORT: '8080'
```

## example role call

```yaml
  - role: docker_deploy
    docker_apps:     "{{ appname_docker_apps }}"
    docker_apps_env: "{{ appname_node_env }}"
    tags:
      - docker_deploy
      - appname

```

Then, in your inventory:

```yaml
prodgroup:
  vars:
    appname_node_env: 'prod'
```
