# Exercicio 8 


## 📦 Parte 1: Persistência com Volumes

### Justificativa
Porque os volumes são mais seguros, portáteis e fáceis de gerenciar que bind mounts, sendo mais recomendados para utilização de dados sensíveis

Comando para criar o volume:
```bash
docker volume create mysql_dados
```

## 🛢️ Parte 2: Container com MySQL

### Configuração do Banco de Dados


```bash
docker run -d --name container-mysql \
  -e MYSQL_ROOT_PASSWORD=root \
  -e MYSQL_DATABASE=exercicio8_db \
  -e MYSQL_USER=fernanda \
  -e MYSQL_PASSWORD=123qwe.\
  -p 3307:3306 \
  -v mysql_dados:/var/lib/mysql \
  mysql:8.0
```

Comando para iniciar o container com o volume (estilo linha única):
```bash
docker run -d --name container-mysql -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=exercicio8_db -e MYSQL_USER=fernanda -e MYSQL_PASSWORD=123qwe. -p 3307:3306 -v mysql_dados:/var/lib/mysql mysql:8.0
```

---


1. Copie o script de inicialização para o container:
   ```bash
   docker cp ./init.sql container-mysql:/docker-entrypoint-initdb.d/init.sql
   ```

2. Acesse o container:
   ```bash
   docker exec -it container-mysql bash
   ```

3. No terminal do container, acesse o MySQL:
   ```bash
   mysql -u root -p
   ```
   Digite a senha `root` quando solicitado.

4. Execute os seguintes comandos no MySQL para inicializar o banco de dados:
   ```sql
   use exercicio8_db; show tables; source /docker-entrypoint-initdb.d/init.sql;
   ```

5. Verifique as tabelas e os dados inseridos:
   ```sql
   use exercicio8_db; show tables; select * from clientes;
   ```

Exemplo de saída:
```plaintext
+----+----------------+------------------+-------------+------------+
| id | nome           | email            | telefone    | endereco   |
+----+----------------+------------------+-------------+------------+
|  1 | Carlos Silva   | carlos@email.com | 11999999999 | Rua A, 123 |
|  2 | Ana Souza      | ana@email.com    | 11988888888 | Rua B, 456 |
|  3 | Pedro Lima     | pedro@email.com  | 11977777777 | Rua C, 789 |
|  4 | Maria Oliveira | maria@email.com  | 11966666666 | Rua D, 101 |
|  5 | João Santos    | joao@email.com   | 11955555555 | Rua E, 202 |
+----+----------------+------------------+-------------+------------+
```

---

## 🧱 Parte 3: Imagem Personalizada

### Criação de Imagens Personalizadas
1. Comite o container para criar imagens personalizadas:
   ```bash
   docker commit container-mysql exercicio8_db:v1
   docker commit container-mysql exercicio8_db:v2
   ```

2. Liste as imagens criadas:
   ```bash
   docker images | Select-String exercicio8_db
   ```

3. Adicione tags às imagens:
   ```bash
   docker tag exercicio8_db:v1 carmonoca/exercicio8_db:v1
   docker tag exercicio8_db:v2 carmonoca/exercicio8_db:v2
   ```

---

## ☁️ Parte 4: Docker Hub

### Publicação das Imagens
1. Faça login no Docker Hub:
   ```bash
   docker login
   ```

2. Envie as imagens para o Docker Hub:
   ```bash
   docker push carmonoca/exercicio8_db:v1
   docker push carmonoca/exercicio8_db:v2
   ```

Links das imagens:
- [Imagens](https://hub.docker.com/repository/docker/carmonoca/exercicio8_db/tags)

---

## 🔁 Parte 5: Testar Persistência

### Demonstração
Os dados foram preservados após reiniciar e recriar o container, graças ao uso do volume `dados_mysql`.

#### Reinicializar o Container
```bash
docker stop container-mysql
docker start container-mysql
```

#### Verificar os Dados Após Reinicialização
```bash
docker exec -it container-mysql bash
mysql -u root -p
use exercicio8_db; show tables; select * from clientes;
```

#### Recriar o Container
```bash
docker stop container-mysql
docker rm container-mysql
docker run -d --name container-mysql -e MYSQL_ROOT_PASSWORD=root -e MYSQL_DATABASE=exercicio8_db -e MYSQL_USER=fernanda -e MYSQL_PASSWORD=123qwe. -p 3307:3306 -v mysql_dados:/var/lib/mysql mysql:8.0
```

#### Verificar os Dados Após Recriação
```bash
docker exec -it container-mysql bash
mysql -u root -p
use exercicio8_db; show tables; select * from clientes;
```

Exemplo de saída:
```plaintext
+----+----------------+------------------+-------------+------------+
| id | nome           | email            | telefone    | endereco   |
+----+----------------+------------------+-------------+------------+
|  1 | Carlos Silva   | carlos@email.com | 11999999999 | Rua A, 123 |
|  2 | Ana Souza      | ana@email.com    | 11988888888 | Rua B, 456 |
|  3 | Pedro Lima     | pedro@email.com  | 11977777777 | Rua C, 789 |
|  4 | Maria Oliveira | maria@email.com  | 11966666666 | Rua D, 101 |
|  5 | João Santos    | joao@email.com   | 11955555555 | Rua E, 202 |
+----+----------------+------------------+-------------+------------+
```