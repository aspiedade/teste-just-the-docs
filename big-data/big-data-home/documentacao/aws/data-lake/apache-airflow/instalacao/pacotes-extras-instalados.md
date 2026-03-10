
[Documentação](../../../../../documentacao.md) > [AWS](../../../../aws.md) > [Data Lake](../../../data-lake.md) > [Apache Airflow](../../apache-airflow.md) > [Instalacao](../instalacao.md)

# Pacotes extras instalados

> [!NOTE]
> **Pacote para fazer chamadas do Athena pelo Airflow**
>
> - pip3 install apache-airflow-backport-providers-amazon
>
> Mais informações em <https://github.com/apache/airflow/tree/master/airflow/providers/amazon#installation>

> [!NOTE]
> **Pacote Python para enviar mensagens para o MS Teams pelo Airflow**
>
> - sudo pip3 install pymsteams
>
> A documentação do projeto em <https://pypi.org/project/pymsteams/>

> [!NOTE]
> **Pacote Python para enviar as metricas para o Prometheus, para monitoração e envio de alertas via Grafana**
>
> - sudo pip3 install airflow-exporter
>
> A documentação do projeto em <https://github.com/epoch8/airflow-exporter>
>
> **Atenção:** se após instalar esse pacote ocorrer o erro de Flask mais ou menos assim:  *blueprint's name collision occurred between blueprints (...) RBACMetrics*
>
> Veja que existe um diretorio "/etc/airflow/plugins/airflow\_exporter/", com os arquivos "prometheus\_exporter.py"  e "\_\_init\_\_.py".
>
> Se existir, tente remover esse diretorio inteiro do servidor (*sudo rm -rf airflow\_exporter/*) além de remove-lo do bucket de artifacts (s3:/uol-datalake-artifacts-prod/airflow/plugins ), caso contrario o job de sync irá copiar o diretorio novamente para o airflow.

> [!NOTE]
> **Instalação do JAVA na máquina do Airflow:**
>
> - sudo yum install java-1.8.0-openjdk
>
> **Atenção:** caso não exista o diretório /usr/share/man/man1 será necessário criá-lo:
>
> - mkdir -p /usr/share/man/man1

> [!NOTE]
> **Operador JDBC do Airflow:**
>
> - pip3 install apache-airflow-backport-providers-jdbc
>
> A documentação do projeto em <https://github.com/apache/airflow/tree/master/airflow/providers/jdbc#installation>

> [!NOTE]
> **Downgrade do JPype1 para a versão 0.6.3:**
>
> - pip3 install JPype1==0.6.3 --force-reinstall
>
> A documentação do problema <https://github.com/baztian/jaydebeapi/issues/99>

> [!NOTE]
> **Pacote Zeep do Python para tratamento de chamadas SOAP:**
>
> - pip3 install zepp==4.0.0
>
> A documentação do projeto em <https://docs.python-zeep.org/en/master/>
