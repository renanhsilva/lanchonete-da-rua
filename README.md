# Lanchonete da Rua

# Para rodar o projeto

Na raiz:

## Construindo a imagem:
```
docker build -t lanchonete-da-rua .
```

## Subindo a aplicação + DB:
```
docker compose up
```

## Conferir se aplicação está de pé:
```
http://localhost:5000/
```

## Rodando em k8s

**Pré requisitos**
- minikube instalado, caso não tenha só baixar uilizando esse [link](https://minikube.sigs.k8s.io/docs/start/)
- helm instalado, caso não tenha só baixar uilizando esse [link](https://helm.sh/docs/intro/install/)
- docker instalado, caso não tenha só baixar uilizando esse [link](https://docs.docker.com/engine/install/)  

O Primeiro passo vai ser inicializar o seu ambiente kubernetes, com esse comando:
```shell
minikube start
```

Proximo passo vai ser criar apontar o docker-cli para a Engine do minikube assim facilitando na hora do build, pois o k8s já terá acesso a imagem. 
Caso seu SO seja linux ou MacOs:
```shell
eval $(minikube docker-env)
```
Caso seja Windows:
```shell
minikube docker-env
```  

Agora vamos fazer o build da imagem a partir do Dockerfile
```shell
docker build -t lanchonete-da-rua .
```
    
Como ja buildamos a imagem nos passos anteriores, a próxima coisa que voce irá fazer é o build da imagem que será enviar a imagem docker para o seu ambiente kubernetes, utlizando o comando abaixo:
```shell
minikube image load lanchonete-da-rua
```

Com o seu ambiente já inicializado e com a imagem da aplicação disponivel podermos prosseguir com a preparação do nosso helm chart personalizado. Entre na pasta `deploy` e rode o seguinte comando para levantar as dependencias do nosso chart.
``` shell
 helm dependency build
```

Caso você queira validar o que está sendo criado e quais yamls estão sendo gerados, pode rodar o comando abaixo, senão pode pular para o próximo comando.
```shell
helm template lanchonete-de-rua .
```

E para finalizar a criação dos nossos pods pode instalar o chart no seu ambiente
```shell
helm install lanchonete-de-rua .
```
**para testar ambiente k8s**

Para testar se a nossa aplicação subiu vamos usar o `kubeclt`, para disparar comandos contra o cluster.

* Comandos: 
    - validar se os pods subiram
      ```shell
        kubectl get pods
      ```
    - conseguir bater na api
      ```shell
        kubectl port-forward deployment/lanchonete-de-rua-deployment 5000:5000
      ```
    - validar criação das secrets
      ```shell
        kubectl get secrets
      ```

# O Problema
Há uma lanchonete de bairro que está expandindo devido seu grande sucesso. Porém, com a expansão e sem um sistema de controle de pedidos, o atendimento aos clientes pode ser caótico e confuso. 

Por exemplo, imagine que um cliente faça um pedido complexo, como um hambúrgues personalizado com ingredientes especificos,  acompanhado de batatas fritas e uma bebida. 
O atendente pode anotar o pedido em um papel e entregá-lo à cosiznha, mas não há garantia de que o pedido será preparado corretamente. 

Sem um sistema de controle de pedidos, pode haver confusão entre os atendentes e a cozinha, resultando em atrasos na preparação e entrega dos pedidos. 

Os pedidos podem ser perdidos, mal interpretados ou esquecidos, levando à insatisfação dos clientes e a perda de negócios. 
Em resumo, um **sistema de controle de pedidos** é essencial para garantir que a lanchonete possa atender os clientes de maneira eficiente, gerenciando seus  pedidos e estoques de forma adequada. 

Sem ele, expandir a lanchonete pode acabar não dando certo, resultando em clientes insatisfeitos e impactando os negocios de forma negativa. 

Para solucionar o problema, a lanchonete irá investir em um sistema de autoatendimento de fast food, que é composto por uma série de dispositivos e interfaces que permitem aos clienetes selecionar e fazer pedidos sem precisar interagir com um aendente, com as seguintes funcionalidades:

## Pedido
* Os clients são apresentados a uma interface de seleção na qual podem optar por se identificarem via CPF, se cadastrarem com nome, email ou não se identificar, podendo montar o combo na seguinte sequencia, sendo todas elas opcionais:
  * Lanche
  * Acompanhamento
  * Bebida
* Em cada etapa é exibido o nome, descrição e preço de cada produto

## Pagamento
O sistema deverá possuir uma opção de pagamento integrada para MVP. A forma de pagamento oferecida será via QRCode do Mercado Pago

## Acompanhamento
Uma vez que o pedido é confirmado e pago, ele é enviado para a cozinha para ser preparado.
Simultaneamente deve aparecer em um monitor para o cliente acompanhar o progresso do seu pedido com as seguintes etapas:

* Recibo
* Em preparação
* Pronto
* Finalizado

## Entrega
Quando o pedido estiver pronto, o sistema deverá notificar o cliente que ele está pronto para retirada. Ao ser retirado, o pedido deve ser atualizado para o status finalizado. Além das etapas do cliente, o estabelecimento precisa de uma acesso administrativo:

* Gerenciar Clientes: com a identificação dos clientes o estabelecimento pode trabalhar em campanhas promocionais
* Gerenciar produtos e categorias: Os produtos dispostos para escolha do cliente serão gerenciados pelo estabelecimento, definindo nome, categoria, preço, descrição e imagens.

Para esse sistema teremos categorias fixas:
  * Lanche
  * Acompanhamento
  * Bebida
  * Sobremesa
* Acompanhamento de pedidos: Deve ser possível acompanhar os pedidos em andamento e tempo de espera de cada pedido.

As informações dispostas no sistema de pedidos precisarão ser gerenciadas pelo estabelecimento através de um painel administrativo

# Entregas da Primeira etapa

+ Documentação do sistema (DDD) utilizando a linguagem ubíqua dos fluxos
  + Realização do pedido e pagamento
  + Preparação e entrega do pedido

+ Uma aplicação para todo sistema de backend (monolito) que deverá ser desenvolvido seguindo os padrões apresentados nas aulas:

  a) Arquitetura hexagonal
  
  b) APIs
     + Cadastro de cliente
     + Identificação do Cliente via CPF
     + Criar, editar e remover produto
     + Buscar produtos por categoria
     + Fake checkout, apenas enviar os produtos para a fila 
     + Listar pedidos

c) Aplicação deverá ser escalável para atender grandes volumes nos horários de pico

d) Banco de dados a escolha da equipe
  * Trabalhar para organizar a fila de pedidos via banco de dados

+ A aplicação deve ser entregue com um Dockerfile configurado para executá-la corretamente.

# Referencias

Build and Deploy Your Flask API With a Postgres Database

https://betterprogramming.pub/cookiecutter-template-to-build-and-deploy-your-flask-api-with-postgres-database-20ad99b8dae4

How to structure a Flask-RESTPlus web service for production builds
https://www.freecodecamp.org/news/structuring-a-flask-restplus-web-service-for-production-builds-c2ec676de563/
