 Como rodar o projeto
=====================

Passo 1: Configurando o ambiente
--------------------------------

### 1.1 Instale o Docker

* Tenha (ou instale) o Docker em sua máquina.

### 1.2 Verifique as portas

* Certifique-se de que as portas a seguir estejam disponíveis para uso, caso contrário, modifique-as:
	+ 9104: mysql-exporter
	+ 6379: redis
	+ 8080: wordpress
	+ 9090: prometheus
	+ 3000: grafana
	+ 8081: cadvisor

Passo 2: Clonar e acessar repositório do projeto
---------------------------------------------

### 2.1 Clone o repositório

* Em qualquer pasta do seu computador, de preferência uma pasta que não possua caracteres especiais.
* Abra o Git Bash e digite o comando: `git clone https://github.com/edgargavioli/dockerwordpress`

### 2.2 Acesse a pasta do projeto

* Com o projeto clonado em sua máquina, acesse a pasta do projeto: `cd trabfelipedocker`

Passo 3: Executar o projeto
-------------------------

### 3.1 Inicialize o Docker Swarm

* Inicialize o Docker Swarm com o comando: `docker swarm init`

### 3.2 Execute o comando de deploy

* Execute o comando: `docker stack deploy -c docker-compose.yml "nome_stack"`
* Este comando iniciará os serviços definidos no arquivo `docker-compose.yml`, incluindo:
	+ mysql: database
	+ mysql-exporter: exporta os dados do mysql para serem acessados pelo prometheus
	+ redis: usado para armazenar em cache as querys feitas pelo wordpress
	+ wordpress: nosso site
	+ prometheus: ferramenta de monitoramento
	+ grafana: transforma os dados do prometheus em dashboards para melhor visualização
	+ cadvisor: ferramenta para monitorar o docker e seus containers

Passo 4: Configurar o WordPress
-----------------------------

### 4.1 Acesse o WordPress

* Abra seu navegador e acesse o link: `http://localhost:8080`

### 4.2 Configure o WordPress

* Configure o WordPress seguindo as instruções.
* Finalizando o processo de configuração do WordPress, você será redirecionado para a página de administrador.

### 4.3 Instale o plugin Redis Object Cache

* Acesse a página de plugins e instale o plugin Redis Object Cache.
* Configure o plugin seguindo as instruções.

Passo 5: Corrigir erro de conexão entre o Redis e o Docker
---------------------------------------------------

### 5.1 Acesse o container do WordPress

* Execute o comando: `docker ps` para listar todos os containers em execução.
* Copie o CONTAINER ID do container que está instanciando o WordPress.
* Execute o comando: `docker exec -it "<ID DO SEU CONTAINER>" bash` para acessar a máquina que está executando o WordPress (Tire as aspas e o simbolo de maior menor ("" e <>) da parte de ID DO SEU CONTAINER para que não haja nenhum erro).

### 5.2 Edite o arquivo wp-config.php

* Execute o comando: `apt update` e `apt install nano` para atualizar o apt e instalar o nano.
* Edite o arquivo `wp-config.php` com o comando: `nano wp-config.php`.
* Adicione as linhas acima da linha que contenha o texto: `/* That's all, stop editing! Happy publishing. */`:
    ```
    define('WP_CACHE', true); 
    define('WP_REDIS_HOST', 'edis'); 
    define('WP_REDIS_PORT', 6379);
    ```
* Salve o arquivo com o comando: "CTRL+O" e feche o arquivo com o comando: "CTRL+X".
* Volte para o painel de plugins do wordpress, atualize a pagina apertando "F5" e veja se o Redis apareça como Acessível.
* Estando assim é só apertar no botão "Ativar o cache de objeto" e o Redis está configurado.

Passo 6: Acessar o Prometheus
-------------------------

### 6.1 Acesse o Prometheus

* Acesse o link: `http://localhost:9090` para acessar a tela inicial do Prometheus.

### 6.2 Verifique as métricas

* Verifique as métricas dos containers e do mysql.
* Métricas do container:
  ```
  container_cpu_system_seconds_total
  container_fs_reads_total
  container_fs_limit_bytes
  ```
* Métricas do MySQL:
  ```
  mysql_exporter_collector_success
  mysql_global_status_connections
  mysql_global_status_max_used_connections
  ```

Passo 7: Dashboard do Grafana
-------------------------

### 7.1 Acesse o Grafana

* Acesse o link: `http://localhost:3000` para acessar a página do Grafana.

### 7.2 Adicione o data source do Prometheus

* Adicione o data source do Prometheus com a URL: `http://prometheus:9090`.

### 7.3 Crie um dashboard

* Pela dashboard vamos em Connections e iremos acessar a opção "Data sources".
* Nessa aba, vamos apertar o botão "Add new data source" e vamos buscar pelo Prometheus.
* Para configurar o data source do prometheus vamos seleciona-lo na lista abrindo assim suas configurações. Em Connection vamos adicionar a url do servidor do prometheus sendo ela http://prometheus:9090.
* Após adicionar sua url vamos no final da pagina e você ira apertar no botão "Save & test" assim podemos ir para a dashboards.
* Na dashboards do grafana vamos apertar em "Create Dashboard" e em seguida "Add visualization".
* No modal que apareceu vamos selecionar o Prometheus.
* Na opção Query, vamos seleiconar a métrica do Prometheus e apertar em run query.
* Assim será gerado um grafico e podemos apertar na opção "Apply" no canto superior direito
* Dashboard do grafana gerado com sucesso!!!

Passo 8: Cadvisor
--------------

### 8.1 Acesse o Cadvisor

* Acesse o link: `http://localhost:8081` para acessar a página do Cadvisor.

### 8.2 Verifique os graficos

* Verifique os graficos dos containers e do docker.
