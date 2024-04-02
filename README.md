## Structure Of this project
```
file structure

Root
├── docker-compose.yml
├── grafana
│   ├── Dockerfile
|   ├── grafana.ini
|   ├── provisioning
│   │   ├── dashboards
|   |   |   ├── dashboard.yml
│   │   ├── datasources
|   |   |   ├── datasource.yml
|   ├── dashboards 
|   |   ├── my-dashboard.json

 ```

# Step 1
create and fill this file :
> __docker-compose.yml__
```yml
version: "3"
services:
# My grafana service 
  grafana:
    build:
      context: ./grafana
    container_name: grafana
    ports:
      - 3000:3000
    volumes:
      - grafana-data:/var/lib/grafana
# Explicitly define the persistent volume for your data storage
volumes:
  grafana-data:
    external: true
```
> __grafana.ini__
```yml
FROM grafana/grafana:7.1.5-ubuntu

# Disable Login form or not
ENV GF_AUTH_DISABLE_LOGIN_FORM "true"
# Allow anonymous authentication or not
ENV GF_AUTH_ANONYMOUS_ENABLED "true"
# Role of anonymous user
ENV GF_AUTH_ANONYMOUS_ORG_ROLE "Admin"
# Install plugins here our in your own config file
# ENV GF_INSTALL_PLUGINS="<list of plugins seperated by ,"

# Add provisioning
ADD ./provisioning /etc/grafana/provisioning
# Add configuration file
ADD ./grafana.ini /etc/grafana/grafana.ini
# Add dashboard json files
ADD ./dashboards /etc/grafana/dashboards
```
> __dashboard.yml__
```yml
# config file version
apiVersion: 1

# list of datasources to insert/update
datasources:
  # <string, required> name of the datasource. Required
- name: My datasource
  # <string, required> datasource type. Required
  type: postgres
  # <string, required> access mode. proxy or direct (Server or Browser in the UI). Required
  access: proxy
  # <int> org id. will default to orgId 1 if not specified
  orgId: 1
  # <string> custom UID which can be used to reference this datasource in other parts of the configuration, if not specified will be generated automatically
  # uid: my_unique_uid
  # <string> url
  url: 
  user: 
  # <string> database name
  database: postgres
  # <bool> allow updating provisioned dashboards from the UI
  allowUiUpdates: true
  # <bool> mark as default datasource. Max one per org
  isDefault: true
  # <map> fields that will be converted to json and stored in jsonData
  jsonData:
    sslmode: 'disable' # disable/require/verify-ca/verify-full
    postgresVersion: 1200 # 903=9.3, 904=9.4, 905=9.5, 906=9.6, 1000=10
    timescaledb: false
  secureJsonData:
    # <string> database password, if used
    password: ""
  version: 1
  # <bool> allow users to edit datasources from the UI.
  editable: true
```
>__datasource.yml__
```yml
# config file version
apiVersion: 1

# list of datasources to insert/update
datasources:
  # <string, required> name of the datasource. Required
- name: My datasource
  # <string, required> datasource type. Required
  type: postgres
  # <string, required> access mode. proxy or direct (Server or Browser in the UI). Required
  access: proxy
  # <int> org id. will default to orgId 1 if not specified
  orgId: 1
  # <string> custom UID which can be used to reference this datasource in other parts of the configuration, if not specified will be generated automatically
  # uid: my_unique_uid
  # <string> url
  url: 
  user: 
  # <string> database name
  database: postgres
  # <bool> allow updating provisioned dashboards from the UI
  allowUiUpdates: true
  # <bool> mark as default datasource. Max one per org
  isDefault: true
  # <map> fields that will be converted to json and stored in jsonData
  jsonData:
    sslmode: 'disable' # disable/require/verify-ca/verify-full
    postgresVersion: 1200 # 903=9.3, 904=9.4, 905=9.5, 906=9.6, 1000=10
    timescaledb: false
  secureJsonData:
    # <string> database password, if used
    password: ""
  version: 1
  # <bool> allow users to edit datasources from the UI.
  editable: true
```
> __grafana.ini__
```yml
[paths]
provisioning = /etc/grafana/provisioning

[server]
enable_gzip = true
# To add HTTPS support:	
#protocol = https	
#;http_addr =	
#http_port = 3000	
#domain = localhost	
#enforce_domain = false	
#root_url = https://localhost:3000	
#router_logging = false	
#static_root_path = public	
#cert_file = /etc/certs/cert.pem	
#cert_key = /etc/certs/cert-key.pem

[security]
# If you want to embed grafana into an iframe for example
allow_embedding = true

[users]
default_theme = dark
```

# Step 2
 In docker compose we set 
```yml 
 volumes:
  grafana-data:
    external: true
```
So: in bash:
```bash
$ docker volume create grafana-data
```
# Step 3
in bash :
```bash
$ docker compose up
```
# Step 4
See:
```bash
http://localhost:3000.
```

# Step 5
For connect Grafana to MySql :> datasources
>> Set : host.docker.internal:3306
