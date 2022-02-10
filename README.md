# Splunk-With-Docker

**OBJETIVO:**

Mostrar o passo a passo para containerização do Splunk e arquivos de configuração da Ferramenta.

Mostrar exemplo de troubleshooting de Logs do Container e ajuste do mesmo.

------------------------------------------------------------------------------------------------------------------------------------------------------------------
**QUICKSTART:**

No DockerHub iremos fazer o pull da image do Splunk, ou seja, baixar localmente a image da aplicação.

*Command Line:* docker pull splunk/splunk

![image](https://user-images.githubusercontent.com/97455314/153449973-283cb97c-3fdc-4e9f-836e-36ee4137043d.png)

Após o pull teremos a image da aplicação para verificar.

*Command Line:* docker images

![image](https://user-images.githubusercontent.com/97455314/153450438-d583d4d5-b925-48ae-9bfa-20363ab0a6a7.png)
------------------------------------------------------------------------------------------------------------------------------------------------------------------
Após iremos iniciar uma instância do Splunk, utilizando alguns argumentos no comando abaixo.

*Command Line:* docker run -d -p 8000:8000 -e "SPLUNK_START_ARGS=--accept-license" -e "SPLUNK_PASSWORD=<password>" --name splunk splunk/splunk:latest

1- *splunk/splunk:latest:* Image baixada pelo pull do repositório do Docker Hub utilizando a TAG latest como a última versão ou a mais recente do Splunk.
  
2- *-p 8000:8000:* Expondo a porta 8000 do Host para utilização da aplicação containerizada.
  
3- *-e "SPLUNK_START_ARGS=--accept-license":* Passando o valor da variable SPLUNK_START_ARGS para aceitar o termo de uso do Splunk.
  
4- *-e "SPLUNK_PASSWORD=#password#":* Passando o valor da variable SPLUNK_PASSWORD para criação do Password do user Admin default. 
  
5- *--name splunk splunk/splunk:latest:* Passando um nome para o container e no final a Image do Pull realizada no Primeiro Passo. 
  
![image](https://user-images.githubusercontent.com/97455314/153455989-44efa683-2c20-410f-b5b7-94b2aebad63e.png)
------------------------------------------------------------------------------------------------------------------------------------------------------------------
Com isso iremos verificar a instância Container e Startar a mesma.
  
Para informar os Containeres criados e visualizar o ID do mesmo.  
  
*Command Line:* docker ps -a
![image](https://user-images.githubusercontent.com/97455314/153454570-7a4c8e52-1d45-4595-8e55-1b9571cdd933.png)
  
Start da Instância e Visualização da mesma em pé.
  
*Command Line:* docker start #CONTAINER ID# && docker ps
![image](https://user-images.githubusercontent.com/97455314/153455181-42aa9460-d7b6-4c5f-804c-ec5b98913d1e.png)
  
------------------------------------------------------------------------------------------------------------------------------------------------------------------
**🚀 INICIANDO O TROUBLESHOOTING - EXEMPLO 🚀**
  
Se analisarmos no inicio do nosso comando *docker run...* e vermos o valor da Variable *SPLUNK_PASSWORD=<password>* que é <password>, iremos analisar que eu passo a senha como *1234*.

💭🤔 Acontece que o Splunk para criação do Password utiliza caracteres ASCII, ou seja, deve conter numerais, letras, caracteres, entre outros.
  
*Doc Wiki com uma explicação ASCII:* https://pt.wikipedia.org/wiki/ASCII
  
Analise Container:

1- Ao utilizar o comando *docker start #CONTAINER ID# && docker ps*, iremos verificar que de fato o container não fica em pé.
  
2- Deveremos analisar os logs do container, pois não iremos conseguir acessar o mesmo para troubleshooting, já que não está em pé.
  
3- Utilizando o comando abaixo, iremos analisar os logs detalhados do container e podemos investigar oque de fato ocorreu.

*Command Line*: docker logs --details #CONTAINER ID#
  
 ![image](https://user-images.githubusercontent.com/97455314/153459593-a6070784-3026-4a46-8c71-aa69df8ac56e.png)

4- Nos trás uma gama de informações, além de detalhes das roles da aplicação, diretório de instalação da mesma, entre outros.
  
5- Rolando mais para baixo do *Log* iremos analisar a seguinte mensagem de erro, informando sobre o Password ASCII informado acima.
![image](https://user-images.githubusercontent.com/97455314/153460191-4ade10f2-7d72-4087-8bcd-0e17c668d75c.png)

**TOP!!! AGORA SABEMOS SOBRE O ERRO E COMO INVESTIGAR**
  
1- Com o erro no momento do Start do Container, deveremos ir até o diretório dentro do File System onde se encontra os conteineres Docker, no meu caso Host LINUX, o diretório default do Docker ao instalá-lo é */var/lib/docker/* no qual existe o diretório *containers* e dentro deste caminho existe apenas um HASH que é a pasta do meu container gerado no *docker run...*.
  
**OBS:** Como tenho apenas um Container, no diretório aparecerá apenas uma pasta com um HASH para acessar as configurações do Container.  
  
![image](https://user-images.githubusercontent.com/97455314/153462361-70bdf824-2f94-4195-9f47-00aa60b023d2.png)
  
2- Dentro do diretório das configs do Container podemos ver acima alguns arquivos, e especificamente dentro de um deles que é o *config.v2.json*, iremos analisar tanto as configurações de diretório da Aplicação Splunk em si, até os Argumentos com os Valores que passamos durante o nosso *docker run...*.
  
3- Podemos executar o comando *cat config.v2.json* para analisar o conteúdo deste arquivo .json, e iremos analisar os valores informados que passamos.
  
![image](https://user-images.githubusercontent.com/97455314/153463405-c6e63def-ea57-4208-82f5-996e5a1cf7a3.png)

4- **Bem D+**. Agora poderemos ajustar esse arquivo seguinte o formato ASCII para o Password.
  
5- Iremos utilizar o **VIM❤️** para editar o arquivo.
  
![image](https://user-images.githubusercontent.com/97455314/153464318-cd987c6f-c491-4bfe-b048-b0e2c3f4c97d.png)

6- Após salvar o arquivo editado, iremos reiniciar o docker com o comando *systemctl restart docker*, rodar o comando *docker start #CONTAINER ID#* e por fim *docker ps* para ver o Container em pé.
 
![image](https://user-images.githubusercontent.com/97455314/153465940-bca1f91c-1efe-4dd7-b99d-7620eeedba60.png)

7- Sendo assim iremos acessar a nossa aplicação *http:/localhost:8000* conforme a porta que definimos.
 
![image](https://user-images.githubusercontent.com/97455314/153466390-b62272ab-3bf3-4471-92bf-910b5aa1274e.png)

8- Agora é só utilizar a Ferramenta, estudar e agregar conhecimento!!!
  
🚀🚀
![image](https://user-images.githubusercontent.com/97455314/153466662-7a37dcba-c972-4e4a-a3f2-4b9cea273722.png)
  
  
  
 
  
  
  
  
  
  
  


