# Exercicio 6 - Meu microserviço

## A- Criar a imagem Docker chamada meu-microservico.
1. Construa a imagem Docker do microserviço:
   ```bash
   docker build -t image-microservico .
   ```

## B- Rodar o container expondo a porta correta.
2. Execute o container do microserviço:
   ```bash
   docker run -d --name container-microservico -p 8080:8080 image-microservico
   ```

## C- Acessar o terminal do container.
3. Para acessar o container em execução:
   ```bash
   docker exec -it container-microservico bash
   ```
4. Verifique o status do microserviço:
   ```bash
   curl http://localhost:8080/status
   ```
   Retorno:
   ```json
   {
     "message": "O microsservico esta funcionando!", 
     "status": "online"
   }
   ```

## D- Verificar se o container está ativo.

5. Verifique os containers em execução:
   ```bash
   docker ps
   ```
   Retorno:
   ```plaintext
   CONTAINER ID   IMAGE                COMMAND           CREATED         STATUS         PORTS                    NAMES
   48bd0da3c62c   image-microservico   "python app.py"   2 minutes ago   Up 2 minutes   0.0.0.0:8080->8080/tcp   container-microservico
   ```
