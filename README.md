<div align="center" id="top">
  <img src=".github/assets/aws-logo.png" alt="AWS Cloud" />
</div>

<h1 align="center">Estudo de caso de arquitetura de software</h1>

<p align="center">
  <a href="#dart-sobre">Sobre</a> &#xa0; | &#xa0;
  <a href="#rocket-principais-tecnologias">Tecnologias</a> &#xa0; | &#xa0;
  <a href="#bar_chart-apresentação">Apresentação</a> &#xa0; | &#xa0;
  <a href="#checkered_flag-começando">Começando</a> &#xa0; | &#xa0;
  <a href="#memo-licença">Licença</a> &#xa0; | &#xa0;
  <a href="https://github.com/Mauricio-Arantes" target="_blank">Autor</a>
</p>

<br>

## :dart: Sobre ##

O objetivo desse projeto é realizar estudos em cima de um desafio proposto que considera uma situação hipotética. 

O estudo se dedica a tratar sobre uma problemática envolvendo três bases de dados distintas, sendo cada qual para atender
demandas distintas. 

O primeiro banco, armazena dados sensíveis e precisa ser muito bem protegido, porém não precisa ser tão rápido de ser lido.

O segundo banco, também armazena dados sensíveis, entretanto não tão sensíveis quanto o anterior, mas precisa de um acesso mais rápido.
Outro ponto característico desta base é que há um serviço de inteligência artificial que irá consumir esses dados para fins de machine learning.

O terceiro banco, não armazena dados sensíveis, porém precisa de um acesso extremamente rápido e performático.

## :rocket: Principais tecnologias ##

<a href="https://aws.amazon.com/pt/">
  <img width="40" title="AWS Cloud" alt="Logo da AWS Cloud" src="https://pbs.twimg.com/profile_images/1351700750993223681/NNJK6vzE_400x400.jpg">
</a> &#xa0; &#xa0;

<a href="https://aws.amazon.com/pt/redis/">
  <img width="40" title="Redis" alt="Logo do Redis" src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/redis/redis-original.svg">
</a> &#xa0; &#xa0;

<a href="https://aws.amazon.com/pt/lambda/">
  <img width="40" title="Lambda" alt="Logo da Lambda" src="https://pics.freeicons.io/uploads/icons/png/20728984181536298169-512.png">
</a> &#xa0; &#xa0;

<a href="https://aws.amazon.com/pt/docker/">
  <img width="40" title="Docker" alt="Logo do Docker" src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/docker/docker-original-wordmark.svg">
</a> &#xa0; &#xa0;

<a href="https://aws.amazon.com/pt/sqs/">
  <img width="40" title="Amazon SQS" alt="Logo do Amazon SQS" src="https://pics.freeicons.io/uploads/icons/png/625237101536207297-512.png">
</a> &#xa0; &#xa0;

<a href="https://www.postgresql.org/">
  <img width="40" title="Postgres" alt="Logo do Postgres" src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/postgresql/postgresql-original-wordmark.svg">
</a> &#xa0; &#xa0;

<a href="https://aws.amazon.com/pt/quickstart/architecture/mongodb/">
  <img width="40" title="Postgres" alt="Logo do Postgres" src="https://cdn.jsdelivr.net/gh/devicons/devicon/icons/mongodb/mongodb-original-wordmark.svg">
</a> &#xa0; &#xa0;

## :bar_chart: Apresentação ##

Iniciando os estudos para propor uma solução diante os desafios para esta arquitetura,
resolvi montar um fluxograma para melhor representar como esses serviços podem se comunicar:

<img src="https://media.discordapp.net/attachments/709159866127614043/933508547050881034/Arquitetura_nuvem.drawio_1.png?width=836&height=676" />

[Link para download para melhor visualização](https://url.gratis/gmn7s7)

## A adoção de micro serviços
A proposta para atender os requisitos do projeto entende que a utilização de uma arquitetura a base de micro-serviços, faz mais sentido para o que procura se atender uma vez que, a preocupação com performance e escalabilidade podem vir a tona muito cedo se utilizando uma API monolítica.

Outro fator que também favorece a escolha de micro serviços como melhor forma de se gerenciar o backend, é sobre a manutenção do código ao decorrer do tempo, pessoas e produto. Uma vez o monolito construído, sua mudança para outro serviço ou para outras propostas de negócio às vezes ficam intrínsecos as funções ou parte de código legado, tornando essa mudança muito mais penosa e cara, tornando a escolha de construir uma nova API sobre a API anterior a melhor opção até que a segunda engula todos os serviços da primeira.

## Utilização de sistema de enfileiramento junto de lambdas
A ideia aqui é utilizar o máximo de processamento síncrono e assíncrono, independente da linguagem escolhida para executar aquela função. Dessa forma, faz necessário de uma API controladora de eventos (Passo 1), para receber as demandas do cliente e direcionar os eventos para um sistema de fila emitir o evento, de modo a uma micro serviço (lambda) receba o evento e execute suas responsabilidades.

## Utilização de cache utilizando Redis vs utilização do ElasticSearch
Embora parecidos possuem usos distintos. Diante a problematica levantada o Redis atende a maioria dos casos, mantendo consistência na entrega dos dados e velocidade.

A importância do cacheamento se faz necessário, a fim de poupar o banco de grandes processos de leitura, o que comumente representa a maioria das operações exercida no banco de dados, novamente solução necessária a ser aplicada quando se pensa em escalabilidade e velocidade.

Entende-se então que, o uso do ElasticSearch faz mais sentido no banco que necessita de entrega de dados em quase tempo real que sofrerá de maior número de operações, esta que pode acabar levando algum tempo, mesmo enfileirada, devido o limite de velocidade de indexação e no caso do MongoDb, tempo de execução dos aggregations. Neste caso, a utilização do ElasticSearch faz mais sentido já que, pode indexar boa parte do banco tornando a pesquisa muito mais rápida, porém mais custosa de ser desenvolvida inicialmente.

Em suma, se não houver necessidade de queries muito pesadas, respostas repetitivas, ou queries (pesquisas) não muito pesadas, o Redis atende melhor e é mais performático que o ElasticSearch nesse sentido. Agora se o cenário for o contrário, a adoção do ElasticSearch faz-se mais sentido.

## Escolha do banco de dados
Sobre a escolha do banco, PostgreSQL é uma das melhores opções no momento. Um banco SQL consegue reunir alta escalabilidade, velocidade de resposta, confidenciabilidade dos dados e baixo custo de operação.

Quando se trata de segurança, o PostgreSQL se mantém como uma boa opção, porém pode não ser tão seguro quando o banco da Oracle, mas seria necessário treinamento específico para a utilização deste banco e mão de obra especializada, talvez pudesse não valer a pena. Desta forma, entendo por convenção que o PostgreSQL é uma escolha segura para crescer com um produto de qualquer escala.

Quanto ao banco de maior performance, logo se pensa em bancos noSql e aqui não foi diferente. A escolha do DocumentDB fornecido pela AWS, possui uma escalabilidade enorme que já é oferecida com esmero pelo Atlas. Com uma capacidade de crescimento horizontal e vertical quase infinita, e também pela enorme velocidade de processamento, acaba tendo como pondo negativo somente quando se trata das dificuldades em queries mais complexas, pois o uso dos aggregations podem passar facilmente mais de 100 linhas dependendo do objetivo. Para tanto, o uso de Views, Indexes e inclusive do ElasticSearch podem auxiliar nesta problematica. Vale ressaltar que o banco pode ser um emissor de eventos, podem disparar lambdas (outros serviços) e até mesmo trabalhar com um banco SQL para garantir maior consistência de dados que seriam melhor com relacionamentos, etc.

## Segurança, escalabilidade e docker
Ao tratar destes tópicos a infra precisa ser bem observada quando construída, o uso de redes privadas garantem que o banco de dados está fora da rede mundial de internet, incapacitando uma invasão direta no banco de dados, porém é possível adicionar mais camadas de proteção, sem muito custo de desempenho, implementando (meio que necessariamente, vez a facilidade de escalabilidade) o docker, cria-se novamente mais uma rede de proteção em cima do postgreSQL, e somente um caminho de conexão entre o mundo “exterior” e o “interior”, aumentando a quantidade de conhecimento específico necessário para acessar o banco.

Toda essa conexão com as redes que precisam ser mais seguras, podem estar protegida com o serviço “KMS” da AWS, uma vez que adiciona mais um nivel de criptografia na transmissão de dados na rede interna, tornando mais complexo a ideia de burlar a API através de uma requisição maliciosa, devido às verificações quanto o requisitor do evento, origem, chaves públicas e chaves privadas, etc.

O lado negativo dessa infraestrutura de segurança inicialmente é o preço, que pode ficar bem alto quando usado em massa (vez que a cobrança é realizada baseado no tempo do processamento e numero de chamadas). Em segundo a performance, devido o tempo necessário para garantir que todas as chaves estão corretas para dar continuidade no pedido, e é agora que o “KMS” age aprimorando a velocidade que este processo de segurança toma.

## Uso do conceito de CQRS para leitura e gravação
Ter diversos serviços consumindo um mesmo banco de dados que serve para leitura e gravação pode ocasionar muitos problemas de performance e até causar inconsitencia no banco de dados por um dump indesejado de dados, e ainda causar diversos problemas de performance. 

A aplicação deste conceito, visa a dividir a responsabilidade de gravação e leitura de forma conceitual e física, o que isto significa? Que os meios para gravar e obter dados diferem, que as consultas são feitas de forma síncrona em uma base dessincronizada (Redis ou ElasticSearch neste caso) e as gravações de forma assíncrona em um banco normalizado (bancos que possuem referencias entre tabelas ou objetos, como PostgreSQL ou MongoDb).

Uma representação gráfica seria a seguinte:

<img title="CQRS Fluxo Simples" src="https://i0.wp.com/www.eduardopires.net.br/wp-content/uploads/2016/07/CQRS_FluxoSimples.jpg?resize=437%2C314&amp;ssl=1" alt="CQRS Fluxo Simples" width="437" height="314">

Este conceito é aplicado somente nos componentes que fazem sentido serem processados desta forma, não se tratando de regra para ser replicada em todo o sistema.

## API baseada em eventos
Uma vez que o sistema é baseado em micro serviços, faz-se sentido que também seja baseada em eventos, pois desta forma a centralização das regras de negócio ficará a base de eventos, estes que podem auxiliar uma equipe de BI para entender melhor o que acontece no projeto, assim como também auxilia a equipe de desenvolvimento a debugar possíveis erros.

Pegando um exemplo, a API inicial pode disparar o primeiro evento a fim de fazer uma pesquisa na Base C, a API irá emitir um evento através da SDK da AWS para adicionar o evento na fila que irá emitir outro evento para quem estiver ouvindo, que então será capturada por uma lambda dentro na rede segura, que disparará o micro serviço correspondente para aquela busca. No momento que a lambda estiver com os dados, é possível retornar esses dados de diversas maneiras para a API original, devolvendo através de Webhooks, Websocket ou HTTP. 

Neste formato descentralizado de API, a API inicial, serve mais como um “porteiro”, para recepcionar e redirecionar as chamadas, tendo seu uso mais pensado em um formado de SDK, para ser instalado nos componentes que dependem do seu uso.

No caso de seu uso for por SDK de um cliente server-side, isso aumenta a segurança da aplicação, vez que novamente a key que de comunicação entre o componente e a API ficará fora de rede pública.
