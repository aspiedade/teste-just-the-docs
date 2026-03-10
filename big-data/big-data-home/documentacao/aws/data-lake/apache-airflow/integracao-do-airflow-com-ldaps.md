
[Documentação](../../../../documentacao.md) > [AWS](../../../aws.md) > [Data Lake](../../data-lake.md) > [Apache Airflow](../apache-airflow.md)

# Integracao do Airflow com LDAPs

A configuração abaixo permite que o usuário que pertence ao grupo "g\_s\_p\_bigdata\_X" se autentique via LDAP, mas com a role de VIEWER. Após a primeira autenticação, outro usuário com perfil de Admin precisa alterar o profile através dos usuários em "Security → List User" do painel do Airflow, para que eles consigam fazer alguma coisa.

**instalar a lib do ldap, pq mesmo tendo a ldap3 instalada pelo airflow, ainda foi necessário esse cara por fora, pois dava erro no ambiente flask, utilizado no Airflow**

```java
sudo su
yum groupinstall "Development tools"
yum install openldap-devel python-devel
pip install python-ldap
```

**subir o certificado do LDAP para esse diretorio e atribuir as permissões pro airflow**

```java
chmod 600 /etc/airflow/certs/ca.crt
chown airflow:airflow /etc/airflow/certs/ca.crt
```

**Editar o /etc/airflow/airflow.cfg**

```java
#alterar rbac e authenticate pra False + comentar a linha de auth_backend
rbac = False
authenticate = False
#auth_backend = airflow.contrib.auth.backends.password_auth
```

**Editar o /etc/airflow/webserver\_config.py** Expandir origem

```java
# -*- coding: utf-8 -*-
#
# Licensed to the Apache Software Foundation (ASF) under one
# or more contributor license agreements.  See the NOTICE file
# distributed with this work for additional information
# regarding copyright ownership.  The ASF licenses this file
# to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance
# with the License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing,
# software distributed under the License is distributed on an
# "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
# KIND, either express or implied.  See the License for the
# specific language governing permissions and limitations
# under the License.
"""Default configuration for the Airflow webserver"""
import os
from flask_appbuilder.security.manager import AUTH_DB
from flask_appbuilder.security.manager import AUTH_LDAP
# from flask_appbuilder.security.manager import AUTH_OAUTH
# from flask_appbuilder.security.manager import AUTH_OID
# from flask_appbuilder.security.manager import AUTH_REMOTE_USER

from airflow import configuration as conf
basedir = os.path.abspath(os.path.dirname(__file__))

# The SQLAlchemy connection string.
SQLALCHEMY_DATABASE_URI = conf.get('core', 'SQL_ALCHEMY_CONN')

# Flask-WTF flag for CSRF
CSRF_ENABLED = True

# ------------------------------------------------------------------------------
# AUTHENTICATION CONFIG
# ------------------------------------------------------------------------------
# For details on how to set up each of the following authentications, see
# http://flask-appbuilder.readthedocs.io/en/latest/security.html# authentication-methods

# The authentication type
#AUTH_TYPE = AUTH_DB
AUTH_TYPE = AUTH_LDAP

AUTH_ROLE_ADMIN = 'Admin'
AUTH_ROLE_PUBLIC = 'Public'
AUTH_USER_REGISTRATION = True
AUTH_USER_REGISTRATION_ROLE = 'Viewer'


#AUTH_LDAP_SERVER = 'ldap://192.168.206.9:389'
AUTH_LDAP_SERVER = 'ldaps://ldap.uolcorp.intranet:636'
AUTH_LDAP_BIND_USER = 'CN=svcacc_bigdata,OU=Users_ServicesAdmin,OU=Usuarios_Especiais,DC=uolcorp,DC=intranet'
AUTH_LDAP_BIND_PASSWORD = 'SENHA_DO_SVCACC_BIGDATA'
AUTH_LDAP_SEARCH = "DC=uolcorp,DC=intranet"
AUTH_LDAP_SEARCH_FILTER = "(memberOf=CN=g_s_p_bigdata_X,OU=Grupos_Permissionamento,OU=Grupos,DC=uolcorp,DC=intranet)"
AUTH_LDAP_USE_TLS = False
AUTH_LDAP_ALLOW_SELF_SIGNED = False
AUTH_LDAP_TLS_CACERTFILE = '/etc/airflow/certs/ca.crt'
AUTH_LDAP_UID_FIELD = 'sAMAccountName'
AUTH_LDAP_FIRSTNAME_FIELD = 'givenName'
AUTH_LDAP_LASTTNAME_FIELD = 'sn'
AUTH_LDAP_EMAIL_FIELD = 'mail'


```

**Reiniciar o serviço**

```java
sudo systemctl restart airflow-webserver
```
