<div align="center">
<img src="https://user-images.githubusercontent.com/47891196/139104117-aa9c2943-37da-4534-a584-e4e5ff5bf69a.png" width="350px" />
</div>

# Introdução Kubernetes

https://www.digitalocean.com/community/tutorials/uma-introducao-ao-kubernetes-pt

`Introdução`

```
Kubernetes é um poderoso sistema open-source, inicialmente desenvolvido pelo Google, para o gerenciamento
de aplicações em container em um ambiente clusterizado. Ele visa fornecer melhores maneiras de gerenciar 
componentes e serviços relacionados e distribuídos em diversas infraestruturas.

Neste guia, vamos discutir alguns conceitos básicos do Kubernetes. Vamos falar sobre a arquitetura do 
sistema, os problemas que ele resolve, e o modelo que ele utiliza para tratar deployments em container 
e escalabilidade.
```

* O que é o Kubernetes?

```
Kubernetes, em seu nível mais básico, é um sistema para executar e coordenar aplicações em container 
através de um cluster de máquinas. É uma plataforma desenhada para gerenciar completamente o ciclo de 
aplicações e serviços em container utilizando métodos que fornecem previsibilidade, escalabilidade, 
e alta disponibilidade.

Como usuário do Kubernetes, você pode definir como as suas aplicações devem rodar e as maneiras pelas 
quais elas devem ser capazes de interagir com outras aplicações ou com o mundo exterior. Você pode 
escalar seus serviços para cima ou para baixo, executar atualizações contínuas elegantemente, e trocar 
tráfego entre diferentes versões de suas aplicações para testar recursos ou reverter deployments 
problemáticos. O Kubernetes fornece interfaces e primitivas de plataformas combináveis que lhe permitem 
definir e gerenciar suas aplicações com alto 
grau de flexibilidade, potência, e confiabilidade.
```

* Arquitetura do Kubernetes

```
Para entender como o Kubernetes é capaz de fornecer esses recursos, é útil ter uma noção de como 
ele é projetado e organizado em alto nível. O Kubernetes pode ser visto como um sistema construído em 
camadas, com cada camada mais alta abstraindo a complexidade encontrada nos níveis mais baixos.

Em sua base, o Kubernetes reúne máquinas físicas ou virtuais individuais em um cluster usando uma rede 
compartilhada para comunicar entre cada servidor. Esse cluster é a plataforma física onde todos os 
componentes, recursos, e cargas de trabalho do Kubernetes são configurados.

Cada uma das máquinas do cluster recebe um papel dentro do ecossistema do Kubernetes. Um servidor 
(ou um pequeno grupo nos deployments de alta disponibilidade) funciona como o servidor mestre. 
Esse servidor age como um gateway e um cérebro para o cluster, expondo uma API para usuários e clientes, 
verificando a saúde de outros servidores, decidindo a melhor forma de dividir e atribuir trabalho 
(conhecido como “scheduling”), e orquestrando a comunicação entre outros componentes. O servidor mestre 
age como o primeiro ponto de contato com o cluster e é responsável pela maior parte da lógica 
centralizada que o Kubernetes fornece.

As outras máquinas no cluster são designadas como nodes ou nós: servidores responsáveis por aceitar 
e executar cargas de trabalho utilizando recursos locais e externos. Para ajudar no isolamento, 
gerenciamento, e flexibilidade, o Kubernetes executa aplicações e serviços em containers, então 
cada node precisa estar equipado com o runtime de container (como o Docker ou rkt). O node recebe 
instruções de trabalho do servidor mestre e cria ou destrói containers de acordo, ajustando as 
regras de rede para rotear e encaminhar o tráfego apropriadamente.

Como mencionado acima, as aplicações e serviços propriamente ditos estão executando no cluster dentro de 
containers. Os componentes subjacentes certificam-se de que o estado desejado das aplicações correspondam 
ao estado real do cluster. Os usuários interagem com o cluster através da comunicação com a API principal 
do servidor, seja diretamente ou através 
de clientes e bibliotecas. Para iniciar uma aplicação ou serviço, um plano declarativo é submetido em 
JSON ou YAML definindo o que criar e como ele deve ser gerenciado. O servidor mestre pega então o plano 
e descobre como executá-lo na infraestrutura através do exame dos requisitos e o estado atual do sistema. 
Esse grupo de aplicativos definidos pelo usuário, em execução de acordo com um plano especificado, 
representa a camada final do Kubernetes.
```

* Componentes do Servidor Mestre

```
Como descrito acima, o servidor mestre age como o plano de controle primário para os clusters do 
Kubernetes. Ele serve como o principal ponto de contato para administradores e usuários, e também fornece 
muitos sistemas em todo o cluster para os nodes de trabalho relativamente pouco sofisticados. No geral, 
os componentes no servidor mestre trabalham juntos para aceitar solicitações de usuários, determinar as 
melhores maneiras de agendar containers de carga de trabalho, autenticar clientes e nodes, ajustar a rede 
de todo o cluster, e gerenciar as responsabilidades de escalabilidade e verificação de saúde.

Estes componentes podem ser instalados em uma única máquina ou distribuídos por vários servidores. 
Vamos dar uma olhada em cada componente individual associado com o servidor mestre nesta seção.
```

`etcd`

```
Um dos componentes fundamentais que o Kubernetes precisa para funcionar é um armazenamento de 
configuração disponível globalmente. O projeto etcd, desenvolvido pelo time da CoreOS, é um armazenamento 
de chave-valor leve e distribuído, que pode ser configurado para se estender por vários nodes.

O Kubernetes utiliza o etcd para armazenar dados de configuração que podem ser acessados por cada um dos 
nodes no cluster. Isso pode ser usado para descoberta de serviços e pode ajudar os componentes a se 
configurarem ou se reconfigurarem de acordo com informações atualizadas. Isso também ajuda a manter o 
estado do cluster com recursos como eleição de líder e bloqueio distribuído. Ao fornecer uma API HTTP/JSON 
simples, a interface para definir ou recuperar valores é muito direta.

Como a maioria dos outros componentes no plano de controle, o etcd pode ser configurado em um único 
servidor mestre ou, em cenários de produção, distribuído entre várias máquinas. O único requisito é que ele 
deve ser acessível via rede para cada uma das máquinas Kubernetes.
```
`kube-apiserver`

```
Um dos serviços mais importantes do servidor mestre é o servidor de API. Este é o principal ponto de 
contato do cluster todo, pois permite que um usuário configure cargas de trabalho e unidades 
organizacionais do Kubernetes. Ele também é responsável por certificar-se de que o armazenamento etcd e 
os detalhes dos serviços dos containers implantados estão de acordo. Ele age como uma ponte entre vários 
componentes para manter a saúde do cluster e disseminar informações e comandos.

O servidor de API implementa uma interface RESTful, o que significa que várias ferramentas distintas e 
bibliotecas podem comunicar-se prontamente com ele. Um cliente chamado kubectl está disponível como 
um método padrão de interação com o cluster Kubernetes a partir de um computador local.
```

`kube-controller-manager`

```
O controller manager é um serviço geral que tem muitas responsabilidades. Primeiramente, ele gerencia 
diferentes controladores que regulam o estado do cluster, gerencia o ciclo de vida das cargas de 
trabalho, e realiza  tarefas rotineiras. Por exemplo, um controlador de replicação assegura que o número 
de réplicas (cópias idênticas) definidas para um pod corresponda ao número atualmente implantado no 
cluster. Os detalhes dessas operações são gravadas no etcd, onde o controller manager observa as alterações 
por meio do servidor da API.

Quando uma alteração é vista, o controlador lê as novas informações e implementa o procedimento que 
preenche o 
estado desejado. Isto pode envolver escalar uma aplicação para cima ou para baixo, ajustar endpoints, etc.
```

`kube-scheduler`

```
O processo que de fato atribui cargas de trabalho a nodes específicos no cluster é o scheduler ou 
agendador. Este serviço lê os requisitos operacionais da carga de trabalho, analisa o ambiente de 
infraestrutura atual, e coloca o trabalho em um node ou nodes aceitáveis.

O scheduler é responsável por rastrear a capacidade disponível em cada host para certificar-se de que as 
cargas de trabalho não estão agendadas para além dos recursos disponíveis. O scheduler deve saber a 
capacidade total bem como os recursos já alocados para cargas de trabalho existentes em cada servidor.
```

`cloud-controller-manager`

```
O Kubernetes pode ser implantado em muitos ambientes diferentes e pode interagir com vários provedores de 
infraestrutura para entender e gerenciar o estado dos recursos no cluster. Como o Kubernetes trabalha com 
representações genéricas de recursos como armazenamento anexável e balanceadores de carga, ele precisa de 
uma forma de mapear estes para os recursos reais fornecidos por provedores de nuvem heterogêneos.

Os cloud controller managers ou gerentes controladores de nuvem agem como a cola que permite o Kubernetes 
interagir com provedores com diferentes capacidades, recursos, e APIs enquanto mantém construções 
relativamente genéricas internamente. Isto permite ao Kubernetes atualizar suas informações de estado 
de acordo com as informações recolhidas a partir do provedor de nuvem, ajustar recursos de nuvem conforme 
as mudanças sejam necessárias no sistema, e criar e usar serviços de nuvem adicionais para satisfazer os 
requisitos de trabalho submetidos ao cluster.
```

* Componentes do Servidor de Nodes

```
No Kubernetes, os servidores que realizam trabalho através da execução de containers são conhecidos como 
nodes. Os servidores de nodes têm alguns requisitos necessários para se comunicar com os componentes do 
mestre, configuração da rede do container, e execução da carga de trabalho real atribuída a eles.
```

* Um Runtime de Container

```
O primeiro componente que cada node deve ter é um runtime de container. Geralmente, este requisito é 
satisfeito através da instalação e execução do Docker, mas alternativas como o rkt e o runc também 
estão disponíveis.

O runtime de container é responsável por iniciar e gerenciar containers, aplicações encapsuladas em um 
ambiente operacional relativamente isolado, mas leve. Cada unidade de trabalho no cluster é, em seu nível 
básico, implementada como um ou mais containers que devem ser implantados. O runtime de container em cada 
node é o componente que finalmente executa os containers definidos na carga de trabalho submetida ao cluster.
```

`kubelet`

```
O principal ponto de contato de cada node com o grupo de cluster é um pequeno serviço chamado kubelet. 
Este serviço é responsável por replicar informações de e para os serviços do plano de controle, bem como 
interagir com o armazenamento etcd para ler detalhes de configuração ou gravar novos valores.

O serviço kubelet comunica-se com os componentes do mestre para autenticar no cluster e receber comandos e 
trabalho. O trabalho é recebido na forma de um manifesto que define a carga de trabalho e os parâmetros 
operacionais. O processo do kubelet então assume a responsabilidade pela manutenção do estado do trabalho 
no servidor de node. Ele controla o runtime de container para lançar ou destruir containers quando 
necessário.
```

`kube-proxy`

```
Para gerenciar sub-redes de hosts individuais e tornar os serviços disponíveis para outros componentes, 
um pequeno serviço de proxy chamado kube-proxy é executado em cada servidor de node. Este processo 
encaminha requisições aos containers corretos, e é geralmente responsável por certificar-se de que o 
ambiente de rede é previsível e acessível, mas isolado quando apropriado.
```

* Objetos e Cargas de Trabalho do Kubernetes

```
Enquanto os containers são o mecanismo subjacente utilizado para implantar aplicações, o Kubernetes 
usa camadas adicionais de abstração sobre a interface do container para fornecer escala, resiliência, e 
recursos de gerenciamento do ciclo de vida. Em vez de gerenciar os containers diretamente, os usuários 
definem e interagem com instâncias compostas de várias primitivas fornecidas pelo modelo de objeto do 
Kubernetes. Analisaremos os diferentes tipos de objetos que podem ser usados para definir essas cargas de 
trabalho abaixo.
```

`Pods`

```
Um pod é a unidade mais básica com a qual o Kubernetes lida. Os containers propriamente ditos não são atribuídos 
a hosts. Em vez disso, um ou mais containers fortemente acoplados são encapsulados em um objeto chamado de pod.

Um pod geralmente representa um ou mais containers que devem ser controlados com uma única aplicação. 
Pods consistem em containers que operam em conjunto, compartilham um ciclo de vida, e devem sempre passar 
pelo scheduling no mesmo node. Eles são gerenciados inteiramente como uma unidade e compartilham seu ambiente, 
volumes, e espaço de IP. A despeito de sua implementação em container, você deve geralmente pensar no pod como 
uma aplicação única, monolítica, para melhor conceituar como o cluster gerenciará os recursos e o 
agendamento do pod.

Geralmente, os pods consistem de um container principal que satisfaz o propósito geral da carga de trabalho e, 
opcionalmente, de alguns containers auxiliares que facilitam tarefas estreitamente relacionadas. 
Estes são programas que se beneficiam de serem executados e gerenciados em seus próprios containers, mas estão 
intimamente ligados ao aplicativo principal. Por exemplo, um pod pode ter um container executando o servidor de 
aplicação primário e um container auxiliar puxando arquivos para o sistema de arquivos compartilhado, quando são 
detectadas mudanças em um repositório externo. O escalonamento horizontal é geralmente desencorajado no nível do 
pod porque existem outros objetos de alto nível mais adequados para a tarefa.

Geralmente, os usuários não devem gerenciar os próprios pods, porque eles não fornecem alguns dos recursos g
eralmente necessários em aplicações (como gerenciamento sofisticado do ciclo de vida e escalonamento). 
Em vez disso, os usuários são encorajados a trabalhar com objetos de alto nível que usam modelos de pod ou pods 
como componentes de base, mas que implementam funcionalidades adicionais.
```

* Controladores de Replicação e Conjuntos de Replicação

```
Frequentemente, ao trabalhar com o Kubernetes, em vez de trabalhar com pods únicos, você estará gerenciando 
grupos de pods idênticos e replicados. Estes são criados a partir de modelos de pod e podem ser escalados 
horizontalmente por controladores conhecidos como Replication Controllers e Replication Sets.

Um Replication Controller ou controlador de replicação é um objeto que define um modelo de pod e os parâmetros 
de controle para escalar réplicas idênticas ou decrementar o número de cópias em execução. Esta é uma maneira 
fácil de distribuir a carga e aumentar a disponibilidade nativamente dentro do Kubernetes. O replication 
controller sabe como criar novos pods quando necessário, porque um modelo que se assemelha a uma definição de 
pod está embutido dentro da configuração dele.

O replication controller é responsável por assegurar que o número de pods implantados no cluster corresponde 
ao número de pods em sua configuração. Se um pod ou host subjacente falhar, o controlador irá iniciar novos 
ods para compensar. Se o número de réplicas na configuração do controlador se alterar, o controlador inicializa 
ou destrói containers para corresponder ao número desejado. Os replication controllers também podem realizar 
atualizações contínuas passando um conjunto de pods para uma nova versão. um a um, minimizando o impacto na 
disponibilidade da aplicação.

Replication Sets ou Conjuntos de Replicação são uma iteração no design do replication controller com maior 
flexibilidade em como o controlador identifica os pods que ele deve gerenciar. Os replication sets estão 
começando a substituir os replication controllers por causa de seus recursos de seleção de réplicas que são 
maiores, mas eles não são capazes de fazer atualizações contínuas para colocar os backends em uma nova versão
como os replication controllers fazem. Em vez disso, os replication sets destinam-se a ser usados dentro de
unidades adicionais de nível superior que fornecem essa funcionalidade.

Assim como os pods, tanto os replication controllers quanto os replication sets raramente são as unidades com
as quais você trabalhará diretamente. Enquanto eles constroem-se em cima do projeto do pod para adicionar 
escalonamento horizontal e garantias de confiabilidade, eles não possuem alguns dos recursos de gerenciamento 
de ciclo de vida refinados encontrados em objetos mais complexos.
```

`Deployments`

```
Deployments são uma das cargas de trabalho mais comuns para se criar e gerenciar diretamente. Os deployments 
usam os replication sets como blocos construtivos, adicionando a funcionalidade de gerenciamento flexível do 
ciclo de vida ao mix.

Embora os deployments criados com replication sets possam parecer duplicar a funcionalidade oferecida pelos 
replication controllers, eles resolvem muitos dos pontos problemáticos que existiam na implementação de 
atualizações contínuas. Ao atualizar aplicativos com replication controllers, os usuários são obrigados a 
enviar um plano para um novo replication controller que substitua o controlador atual. Ao usar replication 
controllers, tarefas como histórico de rastreamento, recuperação de falhas de rede durante a atualização e 
reversão de alterações ruins são difíceis ou deixadas como responsabilidade do usuário.

Deployments são objetos de alto nível projetados para facilitar o gerenciamento do ciclo de vida de pods 
replicados. Os deployments podem ser modificadas facilmente alterando a configuração e o Kubernetes ajustará 
os replication sets, gerenciará transições entre diferentes versões de aplicações, e, opcionalmente, manterá 
o histórico de eventos e irá desfazer recursos automaticamente. Por causa desses recursos, os deployments 
provavelmente serão o tipo de objeto do Kubernetes com o qual você trabalhará com mais frequência.
```

`Stateful Sets`

```
Stateful Sets ou Conjuntos com preservação de estado são pods controladores especializados que oferecem pedidos 
e garantias de exclusividade. Primeiramente, eles são usados para ter um controle mais refinado quando você 
tem requisitos especiais relacionados ao pedido de implantação, dados persistentes ou redes estáveis. 
Por exemplo, stateful sets são geralmente associados a aplicações orientadas a dados, como bancos de dados, 
que precisam de acesso aos mesmos volumes, mesmo se reprogramados para um novo node.

Stateful sets fornecem um identificador de rede estável através da criação de um nome exclusivo baseado em 
número para cada conjunto que persistirá, mesmo se o conjunto precisar ser movido para outro node. 
Da mesma forma, volumes de armazenamento persistentes podem ser transferidos com um pod quando o rescheduling 
é necessário. Os volumes persistem mesmo depois que o pod foi excluído para evitar perda acidental de dados.

Ao implantar ou ajustar a escala, os stateful sets executam operações de acordo com o identificador numerado 
em seu nome. Isso proporciona maior previsibilidade e controle sobre a ordem de execução, 
o que pode ser útil em alguns casos.
```

`Daemon Sets`

```
Daemon Sets são outra forma especializada de controlador de pods que executa uma cópia de um pod em cada node 
no cluster (ou um subconjunto, se especificado). Isso geralmente é útil ao implantar pods que ajudam a executar 
a manutenção e fornecem serviços para os próprios nodes.

Por exemplo, coletar e encaminhar logs, agregar métricas e executar serviços que aumentam os recursos do 
próprio node são candidatos populares para daemon sets. Como os daemon sets geralmente fornecem serviços 
fundamentais e são necessários em toda a frota, eles podem ignorar restrições de scheduling de pods que 
impedem que outros controladores atribuam pods a determinados hosts. Por exemplo, devido às suas 
responsabilidades exclusivas, o servidor mestre é frequentemente configurado para não estar disponível para o 
scheduling normal de pods, mas os daemon sets têm a capacidade de substituir a restrição em uma base de 
pod-por-pod para garantir que os serviços essenciais estejam em execução.
```

`Jobs e Cron Jobs`

```
As cargas de trabalho que descrevemos até agora assumiram um ciclo de vida de longa duração e voltado a 
serviços. O Kubernetes usa uma carga de trabalho chamada jobs para fornecer um fluxo de trabalho baseado 
em tarefas, no qual espera-se que os containers em execução saiam com êxito após algum tempo depois de 
concluírem o seu trabalho. Os jobs são úteis se você precisar executar processamento único ou em lote, em 
vez de executar um serviço contínuo.

Os cron jobs são construídos sobre os jobs. Como os daemons convencionais do cron nos sistemas Linux e 
Unix-like que executam scripts em uma agenda, os cron jobs no Kubernetes fornecem uma interface para executar 
jobs com um componente de agendamento. Os Cron jobs podem ser usados para agendar um trabalho para ser 
executado no futuro ou em uma base regular e recorrente. Os cron jobs do Kubernetes são basicamente uma 
reimplementação do comportamento clássico do cron, usando o cluster como uma plataforma, em vez de um único 
sistema operacional.
```

* Outros Componentes do Kubernetes

```
Além das cargas de trabalho que você pode executar em um cluster, o Kubernetes fornece várias outras abstrações 
que ajudam você a gerenciar seus aplicativos, controlar a rede e ativar a persistência. Vamos discutir alguns 
dos exemplos mais comuns aqui.
```

`Serviços`

```
Até agora, temos usado o termo “serviço” no sentido convencional Unix-like: para denotar processos de 
longa duração, frequentemente conectados em rede, capazes de responder a solicitações. No entanto, no 
Kubernetes, um serviço é um componente que atua como um balanceador de carga básico interno e um embaixador 
para os pods. Um serviço agrupa coleções lógicas de pods que executam a mesma função para apresentá-las 
como uma entidade única.

Isso permite que você implante um serviço que possa rastrear e rotear todos os containers de backend de um
determinado tipo. Os consumidores internos precisam apenas saber o endpoint estável fornecido pelo serviço. 
Enquanto isso, a abstração de serviço lhe permite dimensionar ou substituir as unidades de trabalho de backend 
conforme necessário. O endereço IP de um serviço permanece estável, independentemente das alterações nos pods 
para os quais ele é encaminhado. Ao implantar um serviço, você obtém facilmente a capacidade de descoberta e 
pode simplificar seus projetos de container.

Sempre que você precisar fornecer acesso a um ou mais pods para outro aplicativo ou para consumidores externos, 
você deverá configurar um serviço. Por exemplo, se você tiver um conjunto de pods executando servidores web que 
devem ser acessíveis pela Internet, um serviço fornecerá a abstração necessária. Da mesma forma, se seus 
servidores web precisarem armazenar e recuperar dados, você deverá configurar um serviço interno para fornecer 
acesso aos seus pods de banco de dados.

Embora os serviços, por padrão, só estejam disponíveis usando um endereço IP roteável internamente, eles podem 
ser disponibilizados fora do cluster, escolhendo uma das várias estratégias. A configuração do NodePort funciona 
abrindo uma porta estática na interface de rede externa de cada node. O tráfego para a porta externa será roteado 
automaticamente para os pods apropriados usando um serviço IP de cluster interno.

Alternativamente, o tipo de serviço LoadBalancer cria um balanceador de carga externo para rotear para o serviço 
usando a integração do balanceador de carga do Kubernetes do provedor de nuvem. O cloud controller manager criará 
o recurso apropriado e o configurará usando os endereços de serviço interno.
```

* Volumes e Volumes Persistentes

```
O compartilhamento confiável de dados e a garantia de sua disponibilidade entre reinicializações de containers 
é um desafio em muitos ambientes em container. Os runtimes de container geralmente fornecem algum mecanismo para 
anexar o armazenamento a um container que persiste além da vida útil do container, mas as implementações 
geralmente não possuem flexibilidade.

Para resolver isso, o Kubernetes usa sua própria abstração de volumes, que permite que os dados sejam compartilhados 
por todos os containers dentro de um pod e permaneçam disponíveis até que o pod seja encerrado. Isso significa que 
pods fortemente acoplados podem compartilhar facilmente arquivos sem mecanismos externos complexos. Falhas no container 
dentro do pod não afetarão o acesso aos arquivos compartilhados. Depois que o pod é encerrado, o volume compartilhado 
é destruído, portanto, não é uma boa solução para dados realmente persistentes.

Volumes persistentes são um mecanismo para abstrair armazenamento mais robusto que não está vinculado ao ciclo de 
vida do pod. Em vez disso, eles permitem que os administradores configurem recursos de armazenamento para o cluster 
que os usuários podem solicitar e reivindicar para os pods que estão executando. Depois que um pod é concluído com um 
volume persistente, a política de reivindicação do volume determina se o volume é mantido até ser excluído manualmente 
ou removido imediatamente junto com os dados. Dados persistentes podem ser usados para proteger contra falhas baseadas 
em node e para alocar quantidades maiores de armazenamento do que as disponíveis localmente.
```

* Labels e Annotations

```
Uma abstração organizacional do Kubernetes relacionada, mas fora dos outros conceitos, é a rotulagem. Um label no 
Kubernetes é uma tag semântica que pode ser anexada a objetos do Kubernetes para marcá-los como parte de um grupo. 
Estes podem ser selecionados para segmentar instâncias diferentes para gerenciamento ou roteamento. Por exemplo, cada 
um dos objetos baseados em controlador usa labels para identificar os pods nos quais eles devem operar. Os serviços 
usam labels para entender os pods de backend para os quais devem encaminhar solicitações.

Os labels são fornecidos como pares simples de chave-valor. Cada unidade pode ter mais de um label, mas cada unidade 
só pode ter uma entrada para cada chave. Normalmente, uma chave de “nome” é usada como um identificador de 
propósito geral, mas você também pode classificar objetos por outros critérios, como estágio de desenvolvimento, 
acessibilidade pública, 
versão da aplicação, etc.

Annotations são mecanismos semelhantes que permitem anexar informações arbitrárias de chave-valor a um objeto. 
Enquanto os labels devem ser usados para informações semânticas úteis para corresponder a um pod com critérios de 
seleção, as annotations são mais livres e podem conter dados menos estruturados. Em geral, as annotations são uma 
maneira de adicionar metadados ricos a um objeto que não é útil para fins de seleção.
```
* Conclusão

```
O Kubernetes é um projeto empolgante que permite aos usuários executar cargas de trabalho em containers altamente 
disponíveis e escaláveis em uma plataforma altamente abstrata. Embora a arquitetura e o conjunto de componentes 
internos do Kubernetes possam, a princípio, parecer assustadores, sua potência, flexibilidade e o robusto conjunto de 
recursos são inigualáveis no mundo do código aberto. Compreendendo como os blocos construtivos básicos se encaixam, 
você pode começar a projetar sistemas que aproveitem totalmente os recursos da plataforma para executar e gerenciar 
suas cargas de trabalho em escala.
```
---
---

# Detalhando mais sobre a estrutura do Kubernetes

https://king.host/blog/2018/05/como-usar-kubernetes-na-pratica/

---
---

# Como usar Kubernetes: primeiro objeto da aplicação – POD

No primeiro momento, depois de já ter tudo instalado em sua máquina e o diretório da aplicação criado, 
precisamos criar nosso objeto POD. Vamos criar o arquivo com as configurações do objeto então:

`Arquivo aplicacao.yaml`

```

apiVersion: v1   #versão da API do Kubernetes 
kind: Pod        #Tipo do objeto que será criado
metadata: 
 name: aplicacao #Nome de identificação para o objeto pod
spec:            #Características do objeto
 containers:
   - name: container-aplicacao
     image: php:5.6-apache
     ports:
       - containerPort: 80

```       

Depois de criar o arquivo do objeto com suas especificações, precisamos rodar o comando que de fato é responsável por 
sua criação:     

```
$ kubectl create -f aplicacao.yaml

iago@iagomachado ~/Desktop/Arquivos/kubernetes/app$ kubectl create -f aplicacao.yaml 
pod "aplicacao" created

```

Pronto, agora que temos nosso objeto POD criado, será que já podemos testar uma das funcionalidades/vantagens 
do Kubernetes, 
que é sua regeneração própria? Bom, vamos testar removendo objeto que criamos e ver se o Kubernetes consegue 
regenerar ele sozinho:

```
$ kubectl delete pods aplicacao
$ kubectl get pods

iago@iagomachado ~/Desktop/Arquivos/kubernetes/app$  kubectl delete pods aplicacao 
pod "aplicacao" deleted
iago@iagomachado ~/Desktop/Arquivos/kubernetes/app$  kubectl get pods 
No resources found. 

```

Mas o que aconteceu? O Kubernetes deveria ter recriado o objeto não? Bom, 
acontece que até o momento foi criado apenas o objeto POD, que é um dos objetos mais básicos do kubernetes, 
sendo assim, ele não tem recursos suficientes para informar o Kubernetes do status de processamento da aplicação. 
Para que seja possível é necessário criarmos o objeto deployment, 
que será responsável por atualizar o Kubernetes sobre as informações de processamento.

---

# O objeto Deployment

`Arquivo deployment.yaml`

```

 apiVersion: apps/v1beta1  
  kind: Deployment  
  metadata:  
    name: aplicacao-deployment  
  spec:  
    template:  
      metadata:  
        labels:  
        name: aplicacao-pod  
      spec:  
        containers:  
          - name: container-aplicacao  
            image: php:5.6-apache  
            ports:  
              - containerPort: 80

```              

Com essas configurações, agora estamos colocando nosso objeto POD dentro de outro, o Deployment, 
que por sua vez possui mais recursos e conseguirá então, 
passar o estado desejado que foi configurado para nosso Kubernetes.
É possível ver que o segundo spec é basicamente o spec do nosso objeto POD inicial. 
Agora basta rodar o comando para criar o objeto, em seguida, vamos listar ele:        

```
$ kubectl create -f deployment.yaml 
$ kubectl get pods

iago@iagomachado ~/Desktop/Arquivos/kubernetes/app$  kubectl create -f deployment.yaml 
deployment.apps "aplicacao-deployment" created
iago@iagomachado ~/Desktop/Arquivos/kubernetes/app$  kubectl get pods 
NAME READY STATUS RESTARTS AGE
aplicacao-deployment-787789445d-77l5r 0/1 ContainerCreating 0 7s

```

Agora vamos ver a mágica acontecer, remova seu objeto, 
aguarde alguns segundos e liste novamente utilizando o comando já ensinado e veremos o seguinte resultado:

```
$ kubectl delete pods aplicacao-deployment-787789445d-77l5r
$ kubectl get pods

iago@iagomachado ~/Desktop/Arquivos/kubernetes/app$  kubectl delete pods aplicacao-deployment-787789445d-77l5r 
pod "aplicacao-deployment-787789445d-77l5r" deleted
iago@iagomachado ~/Desktop/Arquivos/kubernetes/app$  kubectl get pods 
NAME READY STATUS RESTARTS AGE
aplicacao-deployment-787789445d-5m6zz 1/1 Running 0 47s

```

Incrível não é mesmo!? 
Isso prova que o Kubernetes efetuou uma regeneração própria recebendo as informações de estado da aplicação. 
Assim que o objeto foi removido ele iniciou a criação de um novo. #kuberneteséofuturo.
Obs: Repare na tag de identificação do objeto marcada em vermelho, elas são diferentes, 
realmente foi criado um novo objeto no lugar do removido.

Depois de tudo isso, queremos poder acessar nossa aplicação Web, mas como fazemos? 
Os objetos PODs são instáveis e sofrem constantes alterações, dessa forma, não podem ser acessados. 
Para isso é necessária a criação de mais um objeto, o objeto service ou, em português, serviço. 
Ele irá funcionar como um balanceador de cargas e nos permitirá o acesso a aplicação.

---

# O objeto Service

`Arquivo servico-aplicacao.yaml`

```

 apiVersion:  v1  
 kind:  Service  
 metadata:  
   name:  servico-aplicacao  
 spec:  
   type: LoadBalancer  
   ports:  
     - port:  80  
   selector:  
     name:  aplicacao-pod

```     

Depois de criar o arquivo vamos rodar o famoso comando responsável por criar o objeto e 
em seguida rodar o comando que nos devolve a URL de acesso a aplicação:  

```
$ kubectl create -f servico-aplicacao.yaml
$ minikube service servico-aplicacao --url

iago@iagomachado ~/Desktop/Arquivos/kubernetes/app$  kubectl create -f servico-aplicacao.yaml 
service "servico-aplicacao" created
iago@iagomachado ~/Desktop/Arquivos/kubernetes/app$  minikube service servico-aplicacao --url 
http://192.168.99.100:31866

```
Acessando a URL http://192.168.99.100:31866 (ou a URL que foi retornada para você) é possível perceber que 
o erro 403 Forbidden foi apresentado na tela (se você entende um pouco de Docker, já deve saber o motivo). 
Muito simples, é por que estamos usando como base a seguinte imagem php:5.6-apache, que não vem com nada no 
diretório html! Agora só precisamos adicionar algum arquivo no objeto deployment para testar. 
Para isso vamos acessar o objeto e criar um arquivo index.php com algum conteúdo:

```
$ kubectl exec -it aplicacao-deployment-787789445d-5m6zz bash
$ echo "<?php phpinfo(); ?>" > index.php

iago@iagomachado ~/Desktop/Arquivos/kubernetes/app$  kubectl exec -it aplicacao-deployment-787789445d-5m6zz bash
root@aplicacao-deployment-787789445d-5m6zz:/var/www/html# echo "<?php phpinfo(); ?>" > index.php

```
Confira se o arquivo realmente foi criado.

```
root@aplicacao-deployment-787789445d-5m6zz:/var/www/html# ls
index.php

```
Agora basta acessar sua aplicação na URL que retornou para você e pronto! Você está rodando uma aplicação 
Web com Kubernetes.

---
---

# Kubernetes-K8S-Orbite

* Instalação Kubernetes:

```
# vim /etc/modules-load.d/k8s.conf
br_netfilter
ip_vs_rr
ip_vs_wrr
ip_vs_sh
nf_conntrack_ipv4
ip_vs


# apt-get update -y && apt-get upgrade -y

# curl -fsSL https://get.docker.com | bash

# apt-get update && apt-get install -y apt-transport-https

# curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -

# echo "deb http://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list

# apt-get update

# apt-get install -y kubelet kubeadm kubectl

# swapoff -a

# vim /etc/fstab

# kubeadm config images pull

# kubeadm init

# mkdir -p $HOME/.kube

# sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config

# sudo chown $(id -u):$(id -g) $HOME/.kube/config

# kubectl apply -f "https://cloud.weave.works/k8s/net?k8s-version=$(kubectl version | base64 | tr -d '\n')"

# kubectl get pods -n kube-system

# kubeadm join --token 39c341.a3bc3c4dd49758d5 IP_DO_MASTER:6443 --discovery-token-ca-cert-hash sha256:37092

```
---
---

* Verificar nodes e informações

```

vagrant@k8s-master:~$ kubectl get nodes
NAME         STATUS   ROLES    AGE    VERSION
k8s-master   Ready    master   6d2h   v1.17.2
node-1       Ready    <none>   6d2h   v1.17.2
node-2       Ready    <none>   6d2h   v1.17.2
vagrant@k8s-master:~$ kubectl describe node k8s-master
Name:               k8s-master
Roles:              master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=k8s-master
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/master=
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    projectcalico.org/IPv4Address: 172.16.1.10/24
                    projectcalico.org/IPv4IPIPTunnelAddr: 192.168.235.192
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Wed, 05 Feb 2020 17:23:12 +0000
Taints:             node-role.kubernetes.io/master:NoSchedule
Unschedulable:      false
Lease:
  HolderIdentity:  k8s-master
  AcquireTime:     <unset>
  RenewTime:       Tue, 11 Feb 2020 20:12:49 +0000
Conditions:
  Type                 Status  LastHeartbeatTime                 LastTransitionTime                Reason                       Message
  ----                 ------  -----------------                 ------------------                ------                       -------
  NetworkUnavailable   False   Tue, 11 Feb 2020 18:33:22 +0000   Tue, 11 Feb 2020 18:33:22 +0000   CalicoIsUp                   Calico is running on this node
  MemoryPressure       False   Tue, 11 Feb 2020 20:09:43 +0000   Wed, 05 Feb 2020 17:23:09 +0000   KubeletHasSufficientMemory   kubelet has sufficient memory available
  DiskPressure         False   Tue, 11 Feb 2020 20:09:43 +0000   Wed, 05 Feb 2020 17:23:09 +0000   KubeletHasNoDiskPressure     kubelet has no disk pressure
  PIDPressure          False   Tue, 11 Feb 2020 20:09:43 +0000   Wed, 05 Feb 2020 17:23:09 +0000   KubeletHasSufficientPID      kubelet has sufficient PID available
  Ready                True    Tue, 11 Feb 2020 20:09:43 +0000   Wed, 05 Feb 2020 17:24:09 +0000   KubeletReady                 kubelet is posting ready status. AppArmor enabled
Addresses:
  InternalIP:  172.16.1.10
  Hostname:    k8s-master
Capacity:
  cpu:                4
  ephemeral-storage:  64194404Ki
  hugepages-2Mi:      0
  memory:             2047672Ki
  pods:               110
Allocatable:
  cpu:                4
  ephemeral-storage:  59161562629
  hugepages-2Mi:      0
  memory:             1945272Ki
  pods:               110
System Info:
  Machine ID:                 5ebf5e5e0b5564f403ddc2e15e39d743
  System UUID:                C7FDDF66-0DD0-4EAA-8382-324569A29918
  Boot ID:                    334bf2e3-e237-49fe-8dd3-19237599b2f5
  Kernel Version:             4.4.0-173-generic
  OS Image:                   Ubuntu 16.04.6 LTS
  Operating System:           linux
  Architecture:               amd64
  Container Runtime Version:  docker://19.3.5
  Kubelet Version:            v1.17.2
  Kube-Proxy Version:         v1.17.2
PodCIDR:                      192.168.0.0/24
PodCIDRs:                     192.168.0.0/24
Non-terminated Pods:          (9 in total)
  Namespace                   Name                                        CPU Requests  CPU Limits  Memory Requests  Memory Limits  AGE
  ---------                   ----                                        ------------  ----------  ---------------  -------------  ---
  kube-system                 calico-kube-controllers-5c45f5bd9f-69qmd    0 (0%)        0 (0%)      0 (0%)           0 (0%)         6d2h
  kube-system                 calico-node-f4stf                           250m (6%)     0 (0%)      0 (0%)           0 (0%)         6d2h
  kube-system                 coredns-6955765f44-22qr6                    100m (2%)     0 (0%)      70Mi (3%)        170Mi (8%)     6d2h
  kube-system                 coredns-6955765f44-5lqwv                    100m (2%)     0 (0%)      70Mi (3%)        170Mi (8%)     6d2h
  kube-system                 etcd-k8s-master                             0 (0%)        0 (0%)      0 (0%)           0 (0%)         6d2h
  kube-system                 kube-apiserver-k8s-master                   250m (6%)     0 (0%)      0 (0%)           0 (0%)         6d2h
  kube-system                 kube-controller-manager-k8s-master          200m (5%)     0 (0%)      0 (0%)           0 (0%)         6d2h
  kube-system                 kube-proxy-68fnz                            0 (0%)        0 (0%)      0 (0%)           0 (0%)         6d2h
  kube-system                 kube-scheduler-k8s-master                   100m (2%)     0 (0%)      0 (0%)           0 (0%)         6d2h
Allocated resources:
  (Total limits may be over 100 percent, i.e., overcommitted.)
  Resource           Requests    Limits
  --------           --------    ------
  cpu                1 (25%)     0 (0%)
  memory             140Mi (7%)  340Mi (17%)
  ephemeral-storage  0 (0%)      0 (0%)
Events:              <none>

```
* Ativando o auto complete

```
root@k8s-master:~# source <(kubectl completion bash)
root@k8s-master:~# echo "source <(kubectl completion bash)" >> /root/.bashrc

```

* Procurar um namespace especifico

```
vagrant@k8s-master:~$ kubectl get namespaces
NAME              STATUS   AGE
default           Active   6d3h
kube-node-lease   Active   6d3h
kube-public       Active   6d3h
kube-system       Active   6d3h
vagrant@k8s-master:~$ kubectl get pods -n kube-system
NAME                                       READY   STATUS    RESTARTS   AGE
calico-kube-controllers-5c45f5bd9f-69qmd   1/1     Running   0          6d3h
calico-node-f4stf                          1/1     Running   0          6d3h
calico-node-wrxb9                          1/1     Running   1          6d2h
calico-node-z4dsg                          1/1     Running   1          6d3h
coredns-6955765f44-22qr6                   1/1     Running   0          6d3h
coredns-6955765f44-5lqwv                   1/1     Running   0          6d3h
etcd-k8s-master                            1/1     Running   1          6d3h
kube-apiserver-k8s-master                  1/1     Running   1          6d3h
kube-controller-manager-k8s-master         1/1     Running   1          6d3h
kube-proxy-68fnz                           1/1     Running   1          6d3h
kube-proxy-h664c                           1/1     Running   1          6d2h
kube-proxy-l596h                           1/1     Running   1          6d3h
kube-scheduler-k8s-master                  1/1     Running   1          6d3h

```

* Usando o parametro -o wide, ele trás mais detalhes

```
vagrant@k8s-master:~$ kubectl get pods -n kube-system -o wide
NAME                                       READY   STATUS    RESTARTS   AGE    IP                NODE         NOMINATED NODE   READINESS GATES
calico-kube-controllers-5c45f5bd9f-69qmd   1/1     Running   0          6d3h   192.168.235.194   k8s-master   <none>           <none>
calico-node-f4stf                          1/1     Running   0          6d3h   172.16.1.10       k8s-master   <none>           <none>
calico-node-wrxb9                          1/1     Running   1          6d2h   172.16.1.12       node-2       <none>           <none>
calico-node-z4dsg                          1/1     Running   1          6d3h   172.16.1.11       node-1       <none>           <none>
coredns-6955765f44-22qr6                   1/1     Running   0          6d3h   192.168.235.193   k8s-master   <none>           <none>
coredns-6955765f44-5lqwv                   1/1     Running   0          6d3h   192.168.235.195   k8s-master   <none>           <none>
etcd-k8s-master                            1/1     Running   1          6d3h   172.16.1.10       k8s-master   <none>           <none>
kube-apiserver-k8s-master                  1/1     Running   1          6d3h   172.16.1.10       k8s-master   <none>           <none>
kube-controller-manager-k8s-master         1/1     Running   1          6d3h   172.16.1.10       k8s-master   <none>           <none>
kube-proxy-68fnz                           1/1     Running   1          6d3h   172.16.1.10       k8s-master   <none>           <none>
kube-proxy-h664c                           1/1     Running   1          6d2h   172.16.1.12       node-2       <none>           <none>
kube-proxy-l596h                           1/1     Running   1          6d3h   172.16.1.11       node-1       <none>           <none>
kube-scheduler-k8s-master                  1/1     Running   1          6d3h   172.16.1.10       k8s-master   <none>           <none>

```
---
---

# Como Executar um pod e verificar sua estrutura

```
vagrant@k8s-master:~$ kubectl run nginx --image nginx
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.
deployment.apps/nginx created

vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           59s

vagrant@k8s-master:~$ kubectl get replicasets.
NAME               DESIRED   CURRENT   READY   AGE
nginx-6db489d4b7   1         1         1       103s

vagrant@k8s-master:~$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6db489d4b7-wkpqj   1/1     Running   0          2m7s

vagrant@k8s-master:~$ kubectl get services
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   6d3h

vagrant@k8s-master:~$ kubectl describe replicasets. nginx-6db489d4b7
Name:           nginx-6db489d4b7
Namespace:      default
Selector:       pod-template-hash=6db489d4b7,run=nginx
Labels:         pod-template-hash=6db489d4b7
                run=nginx
Annotations:    deployment.kubernetes.io/desired-replicas: 1
                deployment.kubernetes.io/max-replicas: 2
                deployment.kubernetes.io/revision: 1
Controlled By:  Deployment/nginx
Replicas:       1 current / 1 desired
Pods Status:    1 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  pod-template-hash=6db489d4b7
           run=nginx
  Containers:
   nginx:
    Image:        nginx
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age    From                   Message
  ----    ------            ----   ----                   -------
  Normal  SuccessfulCreate  4m52s  replicaset-controller  Created pod: nginx-6db489d4b7-wkpqj

```

```
vagrant@k8s-master:~$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6db489d4b7-wkpqj   1/1     Running   0          8m15s
vagrant@k8s-master:~$ kubectl describe pods nginx-6db489d4b7-wkpqj
Name:         nginx-6db489d4b7-wkpqj
Namespace:    default
Priority:     0
Node:         node-2/172.16.1.12
Start Time:   Tue, 11 Feb 2020 20:32:31 +0000
Labels:       pod-template-hash=6db489d4b7
              run=nginx
Annotations:  cni.projectcalico.org/podIP: 192.168.247.1/32
Status:       Running
IP:           192.168.247.1
IPs:
  IP:           192.168.247.1
Controlled By:  ReplicaSet/nginx-6db489d4b7
Containers:
  nginx:
    Container ID:   docker://09c060755f6b21f60084e646279813a2ccd61b1991bfdb7d108d4f5f3eb17a08
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:ad5552c786f128e389a0263104ae39f3d3c7895579d45ae716f528185b36bc6f
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Tue, 11 Feb 2020 20:32:54 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-t4hfb (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-t4hfb:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-t4hfb
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age        From               Message
  ----    ------     ----       ----               -------
  Normal  Scheduled  <unknown>  default-scheduler  Successfully assigned default/nginx-6db489d4b7-wkpqj to node-2
  Normal  Pulling    8m49s      kubelet, node-2    Pulling image "nginx"
  Normal  Pulled     8m30s      kubelet, node-2    Successfully pulled image "nginx"
  Normal  Created    8m30s      kubelet, node-2    Created container nginx
  Normal  Started    8m29s      kubelet, node-2    Started container nginx

```

```
vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           12m
vagrant@k8s-master:~$ kubectl describe deployments. nginx
Name:                   nginx
Namespace:              default
CreationTimestamp:      Tue, 11 Feb 2020 20:32:31 +0000
Labels:                 run=nginx
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               run=nginx
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  run=nginx
  Containers:
   nginx:
    Image:        nginx
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-6db489d4b7 (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  13m   deployment-controller  Scaled up replica set nginx-6db489d4b7 to 1

```

* Executanto scale no nginx

```
vagrant@k8s-master:~$ kubectl scale --replicas=10 deployment nginx
deployment.apps/nginx scaled

vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   2/10    10           2           16m

vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   7/10    10           7           16m

vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   10/10   10           10          16m

```

```
vagrant@k8s-master:~$ kubectl run nginx3 --image nginx
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.
deployment.apps/nginx3 created
vagrant@k8s-master:~$ kubectl get deployments.
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
nginx    10/10   10           10          18m
nginx3   1/1     1            1           6s

```
---
---
# Como remover um deployment

```
vagrant@k8s-master:~$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6db489d4b7-49tzb   1/1     Running   0          4m47s
nginx-6db489d4b7-9rfhk   1/1     Running   0          4m47s
nginx-6db489d4b7-cttdx   1/1     Running   0          4m47s
nginx-6db489d4b7-gfxwn   1/1     Running   0          4m47s
nginx-6db489d4b7-m8xgf   1/1     Running   0          4m47s
nginx-6db489d4b7-nwn7c   1/1     Running   0          4m47s
nginx-6db489d4b7-pf9zc   1/1     Running   0          4m47s
nginx-6db489d4b7-rjzwx   1/1     Running   0          4m47s
nginx-6db489d4b7-wkpqj   1/1     Running   0          20m
nginx-6db489d4b7-x9bw9   1/1     Running   0          4m47s
nginx3-8f65b44f9-s6tlb   1/1     Running   0          2m19s

vagrant@k8s-master:~$ kubectl get deployments.
NAME     READY   UP-TO-DATE   AVAILABLE   AGE
nginx    10/10   10           10          20m
nginx3   1/1     1            1           2m31s
vagrant@k8s-master:~$ kubectl delete deployments. nginx
deployment.apps "nginx" deleted
vagrant@k8s-master:~$ kubectl delete deployments. nginx3
deployment.apps "nginx3" deleted
vagrant@k8s-master:~$ kubectl get deployments.
No resources found in default namespace.
vagrant@k8s-master:~$ 

```
---
---

# Direcionando uma porta

```
vagrant@k8s-master:~$ kubectl run nginx --image nginx --port=80
kubectl run --generator=deployment/apps.v1 is DEPRECATED and will be removed in a future version. Use kubectl run --generator=run-pod/v1 or kubectl create instead.
deployment.apps/nginx created

vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   0/1     1            0           4s

```
* Validando porta atrelada

```
vagrant@k8s-master:~$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
nginx-5578584966-6bh78   1/1     Running   0          3m25s

vagrant@k8s-master:~$ kubectl describe pods nginx-5578584966-6bh78
Name:         nginx-5578584966-6bh78
Namespace:    default
Priority:     0
Node:         node-1/172.16.1.11
Start Time:   Tue, 11 Feb 2020 20:56:51 +0000
Labels:       pod-template-hash=5578584966
              run=nginx
Annotations:  cni.projectcalico.org/podIP: 192.168.84.135/32
Status:       Running
IP:           192.168.84.135
IPs:
  IP:           192.168.84.135
Controlled By:  ReplicaSet/nginx-5578584966
Containers:
  nginx:
    Container ID:   docker://e465a521e4879b442a507ecb57894d3030345fa60d7cf20ceee524224e334a35
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:ad5552c786f128e389a0263104ae39f3d3c7895579d45ae716f528185b36bc6f
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Tue, 11 Feb 2020 20:56:57 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-t4hfb (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-t4hfb:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-t4hfb
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age        From               Message
  ----    ------     ----       ----               -------
  Normal  Scheduled  <unknown>  default-scheduler  Successfully assigned default/nginx-5578584966-6bh78 to node-1
  Normal  Pulling    3m42s      kubelet, node-1    Pulling image "nginx"
  Normal  Pulled     3m39s      kubelet, node-1    Successfully pulled image "nginx"
  Normal  Created    3m39s      kubelet, node-1    Created container nginx
  Normal  Started    3m38s      kubelet, node-1    Started container nginx

```

```
vagrant@k8s-master:~$ kubectl get deployments. nginx -o yaml

```

`Padrão yaml`:

apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: "2020-02-11T20:56:51Z"
  generation: 1
  labels:
    run: nginx
  name: nginx
  namespace: default
  resourceVersion: "187243"
  selfLink: /apis/apps/v1/namespaces/default/deployments/nginx
  uid: 3a6ba262-a84d-4206-9fbe-c1dad8774fcb
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      run: nginx
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 1
  conditions:
  - lastTransitionTime: "2020-02-11T20:56:57Z"
    lastUpdateTime: "2020-02-11T20:56:57Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2020-02-11T20:56:51Z"
    lastUpdateTime: "2020-02-11T20:56:57Z"
    message: ReplicaSet "nginx-5578584966" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 1
  readyReplicas: 1
  replicas: 1
  updatedReplicas: 1

  ```
  vagrant@k8s-master:~$ kubectl get deployments. nginx -o yaml > primeiro_deployment.yaml

  ```
* `yaml alterado`:

```
vagrant@k8s-master:~$ cat primeiro_deployment.yaml 
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
  generation: 1
  labels:
    run: nginx
  name: my-nginx
  namespace: default
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      run: nginx
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30

```
---
---
#  Criando o deployments

```
vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           19m

vagrant@k8s-master:~$ kubectl create -f primeiro_deployment.yaml 
deployment.apps/my-nginx created

vagrant@k8s-master:~$ kubectl get deployments.
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   1/1     1            1           26s
nginx      1/1     1            1           20m

```
---
---
# Removendo o deployments

```
vagrant@k8s-master:~$ kubectl delete -f primeiro_deployment.yaml 
deployment.apps "my-nginx" deleted
vagrant@k8s-master:~$ kubectl get deployments.
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           22m

```
---
---
# Subindo com 10 replicas o deployments

```
vagrant@k8s-master:~$ vim primeiro_deployment.yaml

```
`Alterar apenas`
spec:
  progressDeadlineSeconds: 600
  replicas: 1

  --->

  spec:
  progressDeadlineSeconds: 600
  replicas: 10

```
vagrant@k8s-master:~$ kubectl create -f primeiro_deployment.yaml 
deployment.apps/my-nginx created

vagrant@k8s-master:~$ kubectl get deployments.
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   2/10    10           2           10s
nginx      1/1     1            1           25m

vagrant@k8s-master:~$ kubectl get deployments.
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   6/10    10           6           30s
nginx      1/1     1            1           25m

vagrant@k8s-master:~$ kubectl get deployments.
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   10/10   10           10          49s
nginx      1/1     1            1           26m

```
---
---

# Criando um Services, para acessar interno

```
vagrant@k8s-master:~$ kubectl expose deployment my-nginx
service/my-nginx exposed

vagrant@k8s-master:~$ kubectl get services
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP   6d4h
my-nginx     ClusterIP   10.107.62.74   <none>        80/TCP    2m45s

```
---
---

# Criando um NodePort, para acessar externo

```
vagrant@k8s-master:~$ kubectl delete service my-nginx
service "my-nginx" deleted

vagrant@k8s-master:~$ kubectl expose deployment my-nginx --type=NodePort
service/my-nginx exposed

vagrant@k8s-master:~$ kubectl get services
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP        6d4h
my-nginx     NodePort    10.97.65.173   <none>        80:31964/TCP   12s

```
---
---

# Criando primeiro Services

`Base`

```
vagrant@k8s-master:~$ kubectl get services -o yaml
apiVersion: v1
items:
- apiVersion: v1
  kind: Service
  metadata:
    creationTimestamp: "2020-02-05T17:23:13Z"
    labels:
      component: apiserver
      provider: kubernetes
    name: kubernetes
    namespace: default
    resourceVersion: "148"
    selfLink: /api/v1/namespaces/default/services/kubernetes
    uid: 5e8959cb-bf2c-44d6-848e-72bc3822589d
  spec:
    clusterIP: 10.96.0.1
    ports:
    - name: https
      port: 443
      protocol: TCP
      targetPort: 6443
    sessionAffinity: None
    type: ClusterIP
  status:
    loadBalancer: {}
- apiVersion: v1
  kind: Service
  metadata:
    creationTimestamp: "2020-02-12T19:30:00Z"
    labels:
      run: nginx
    name: nginx
    namespace: default
    resourceVersion: "354618"
    selfLink: /api/v1/namespaces/default/services/nginx
    uid: 9b94f2c2-18c7-43d3-8c1e-35e0f6279733
  spec:
    clusterIP: 10.99.40.168
    ports:
    - port: 80
      protocol: TCP
      targetPort: 80
    selector:
      run: nginx
    sessionAffinity: None
    type: ClusterIP
  status:
    loadBalancer: {}
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""

vagrant@k8s-master:~$ kubectl get services nginx -o yaml
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2020-02-12T19:30:00Z"
  labels:
    run: nginx
  name: nginx
  namespace: default
  resourceVersion: "354618"
  selfLink: /api/v1/namespaces/default/services/nginx
  uid: 9b94f2c2-18c7-43d3-8c1e-35e0f6279733
spec:
  clusterIP: 10.99.40.168
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}
 
```
---
---

# Criando primeiro Services tipo ClusterIP

```
vagrant@k8s-master:~$ kubectl get services nginx -o yaml > my_first_service.yaml
vagrant@k8s-master:~$ vim my_first_service.yaml 


apiVersion: v1
kind: Service
metadata:
  labels:
    run: nginx
  name: nginx
  namespace: default
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx
  sessionAffinity: None
  type: ClusterIP

vagrant@k8s-master:~$ vim my_first_service.yaml


vagrant@k8s-master:~$ kubectl get services
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP   7d2h
nginx        ClusterIP   10.99.40.168   <none>        80/TCP    100s

```
---
---

# Criando primeiro Services tipo NodePort

```

vagrant@k8s-master:~$ kubectl get service nginx -o yaml > my_first_service_nodeport.yaml

apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2020-02-13T19:31:52Z"
  labels:
    run: nginx
  name: nginx
  namespace: default
spec:
  clusterIP: 10.103.3.243
  externalTrafficPolicy: Cluster
  ports:
  - nodePort: 30260
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx
  sessionAffinity: None
  type: NodePort

vagrant@k8s-master:~$ kubectl create -f my_first_service_nodeport.yaml 
service/nginx created

vagrant@k8s-master:~$ kubectl get services
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP        8d
nginx        NodePort    10.103.3.243   <none>        80:30260/TCP   26s
  
```
---
---
# Criando primeiro Services tipo LoadBalancer

```
vagrant@k8s-master:~$ kubectl expose deployment nginx --type=LoadBalancer
service/nginx exposed

vagrant@k8s-master:~$ kubectl get services
NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP      10.96.0.1      <none>        443/TCP        8d
nginx        LoadBalancer   10.97.37.131   <pending>     80:31596/TCP   9s

vagrant@k8s-master:~$ kubectl get endpoints
NAME         ENDPOINTS                             AGE
kubernetes   172.16.1.10:6443                      8d
nginx        192.168.247.27:80,192.168.84.157:80   64s

vagrant@k8s-master:~$ kubectl get services nginx -o yaml > my_first_service_loadbalancer.yaml

vagrant@k8s-master:~$ vim my_first_service_loadbalancer.yaml

apiVersion: v1
kind: Service
metadata:
  labels:
    run: nginx
  name: nginx
  namespace: default
spec:
  externalTrafficPolicy: Cluster
  ports:
  - nodePort: 31596
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx
  sessionAffinity: None
  type: LoadBalancer


vagrant@k8s-master:~$ kubectl delete service nginx
service "nginx" deleted

vagrant@k8s-master:~$ kubectl create -f my_first_service_loadbalancer.yaml 
service/nginx created

vagrant@k8s-master:~$ kubectl get services
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP      10.96.0.1        <none>        443/TCP        8d
nginx        LoadBalancer   10.108.158.195   <pending>     80:31596/TCP   15s

vagrant@k8s-master:~$ kubectl get endpoints
NAME         ENDPOINTS                             AGE
kubernetes   172.16.1.10:6443                      8d
nginx        192.168.247.27:80,192.168.84.157:80   3m6s

vagrant@k8s-master:~$ kubectl describe endpoints nginx
Name:         nginx
Namespace:    default
Labels:       run=nginx
Annotations:  endpoints.kubernetes.io/last-change-trigger-time: 2020-02-13T20:15:18Z
Subsets:
  Addresses:          192.168.247.27,192.168.84.157
  NotReadyAddresses:  <none>
  Ports:
    Name     Port  Protocol
    ----     ----  --------
    <unset>  80    TCP

Events:  <none>

```

```
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
  labels:
    run: nginx
  name: my-nginx
  namespace: default
spec:
  progressDeadlineSeconds: 600
  replicas: 10
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      terminationGracePeriodSeconds: 30

      vagrant@k8s-master:~$ kubectl get pods
NAME                        READY   STATUS    RESTARTS   AGE
my-nginx-5578584966-6ctvd   1/1     Running   0          15h
my-nginx-5578584966-cpj65   1/1     Running   0          15h
my-nginx-5578584966-d2f7z   1/1     Running   0          15h
my-nginx-5578584966-ghdx9   1/1     Running   0          15h
my-nginx-5578584966-rp9k7   1/1     Running   0          15h
my-nginx-5578584966-scplj   1/1     Running   0          15h
my-nginx-5578584966-sg8ss   1/1     Running   0          15h
my-nginx-5578584966-twr8g   1/1     Running   0          15h
my-nginx-5578584966-xkghg   1/1     Running   0          15h
my-nginx-5578584966-zh2z5   1/1     Running   0          15h

```
---
---
# Deployment e Services tudo junto

```
vagrant@k8s-master:~$ ls
my_first_deployment.yaml  my_first_service.yaml  my_first_service_loadbalancer.yaml  my_first_service_nodeport.yaml

vagrant@k8s-master:~$ cat my_first_service_nodeport.yaml 
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2020-02-13T19:31:52Z"
  labels:
    run: nginx
  name: nginx
  namespace: default
spec:
  clusterIP: 10.103.3.243
  externalTrafficPolicy: Cluster
  ports:
  - nodePort: 30260
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx
  sessionAffinity: None
  type: NodePort

vagrant@k8s-master:~$ vim my_first_deployment.yaml 

```
`copia e deixa assim:`

```
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
  labels:
    run: nginx
  name: my-nginx
  namespace: default
spec:
  replicas: 10
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      terminationGracePeriodSeconds: 30
---
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2020-02-13T19:31:52Z"
  labels:
    run: nginx
  name: nginx
  namespace: default
spec:
  clusterIP: 10.103.3.243
  externalTrafficPolicy: Cluster
  ports:
  - nodePort: 30260
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx
  sessionAffinity: None
  type: NodePort

```

```
vagrant@k8s-master:~$ kubectl delete deployments. my-nginx
deployment.apps "my-nginx" deleted

vagrant@k8s-master:~$ kubectl delete service nginx
service "nginx" deleted

vagrant@k8s-master:~$ kubectl delete -f my_first_deployment.yaml
deployment.apps "my-nginx" deleted

vagrant@k8s-master:~$ kubectl create -f my_first_deployment.yaml 
deployment.apps/my-nginx created
service/nginx created

```

---
---

# Recursos limitados

```
vagrant@k8s-master:~$ cp my_first_deployment.yaml deployment_limited.yaml
vagrant@k8s-master:~$ vim deployment_limited.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
  labels:
    run: nginx
  name: my-nginx
  namespace: default
spec:
  replicas: 10
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP
        resources: 
          limits:
            memory: 512Mi
            cpu: "500m"
          requests:
            memory: 256Mi
            cpu: "250m"
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      terminationGracePeriodSeconds: 30

```

```
vagrant@k8s-master:~$ kubectl create -f deployment_limited.yaml
deployment.apps/my-nginx created

vagrant@k8s-master:~$ kubectl get deployments
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   10/10   10           10          84s

vagrant@k8s-master:~$ kubectl describe deployments. my-nginx
Name:                   my-nginx
Namespace:              default
CreationTimestamp:      Fri, 14 Feb 2020 13:59:46 +0000
Labels:                 run=nginx
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               run=nginx
Replicas:               10 desired | 10 updated | 10 total | 10 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  run=nginx
  Containers:
   nginx:
    Image:      nginx
    Port:       80/TCP
    Host Port:  0/TCP
    Limits:
      cpu:     500m
      memory:  512Mi
    Requests:
      cpu:        250m
      memory:     256Mi
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   my-nginx-75d484d94b (10/10 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  2m20s  deployment-controller  Scaled up replica set my-nginx-75d484d94b to 10


vagrant@k8s-master:~$ kubectl create -f my_first_service.yaml
service/nginx created

vagrant@k8s-master:~$ kubectl get services
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP   8d
nginx        ClusterIP   10.105.12.65   <none>        80/TCP    15s

```

* Editando um arquivo 

```
vagrant@k8s-master:~$ kubectl edit service nginx

# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2020-02-14T14:04:18Z"
  labels:
    run: nginx
  name: nginx
  namespace: default
  resourceVersion: "714681"
  selfLink: /api/v1/namespaces/default/services/nginx
  uid: c359e34f-7f05-49d8-89bd-e04606fdc674
spec:
  clusterIP: 10.105.12.65
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx
  sessionAffinity: None
  type: ClusterIP
status:
  loadBalancer: {}

```
`Editado:`

```
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2020-02-14T14:04:18Z"
  labels:
    run: nginx
  name: nginx
  namespace: default
  resourceVersion: "714681"
  selfLink: /api/v1/namespaces/default/services/nginx
  uid: c359e34f-7f05-49d8-89bd-e04606fdc674
spec:
  clusterIP: 10.105.12.65
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx
  sessionAffinity: None
  type: NodePort
status:
  loadBalancer: {}

vagrant@k8s-master:~$ kubectl edit service nginx
service/nginx edited

```

```
vagrant@k8s-master:~$ kubectl get services
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP        8d
nginx        NodePort    10.105.12.65   <none>        80:30376/TCP   6m6s

```
* Editando porta ou mais o que precisar alterar

```
# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
kind: Service
metadata:
  creationTimestamp: "2020-02-14T14:04:18Z"
  labels:
    run: nginx
  name: nginx
  namespace: default
  resourceVersion: "715211"
  selfLink: /api/v1/namespaces/default/services/nginx
  uid: c359e34f-7f05-49d8-89bd-e04606fdc674
spec:
  clusterIP: 10.105.12.65
  externalTrafficPolicy: Cluster
  ports:
  - nodePort: 30376
    port: 80
    protocol: TCP
    targetPort: 80
  selector:
    run: nginx
  sessionAffinity: None
  type: NodePort
status:
  loadBalancer: {}

vagrant@k8s-master:~$ kubectl edit service nginx
service/nginx edited

vagrant@k8s-master:~$ kubectl get services
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP        8d
nginx        NodePort    10.105.12.65   <none>        80:31376/TCP   11m

vagrant@k8s-master:~$ kubectl get  replicasets.
NAME                  DESIRED   CURRENT   READY   AGE
my-nginx-75d484d94b   10        10        10      17m

vagrant@k8s-master:~$ kubectl edit deployment my-nginx
deployment.apps/my-nginx edited

vagrant@k8s-master:~$ kubectl get replicasets.
NAME                  DESIRED   CURRENT   READY   AGE
my-nginx-75d484d94b   4         4         4       19m

```

```
vagrant@k8s-master:~$ kubectl get deployments.
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   4/4     4            4           3h44m
vagrant@k8s-master:~$ kubectl get pods
NAME                        READY   STATUS    RESTARTS   AGE
my-nginx-75d484d94b-f7rrk   1/1     Running   0          3h44m
my-nginx-75d484d94b-fhb4p   1/1     Running   0          3h44m
my-nginx-75d484d94b-l68gb   1/1     Running   0          3h44m
my-nginx-75d484d94b-zjgzv   1/1     Running   0          3h44m
vagrant@k8s-master:~$ kubectl get pods -o wide
NAME                        READY   STATUS    RESTARTS   AGE     IP               NODE     NOMINATED NODE   READINESS GATES
my-nginx-75d484d94b-f7rrk   1/1     Running   0          3h44m   192.168.84.183   node-1   <none>           <none>
my-nginx-75d484d94b-fhb4p   1/1     Running   0          3h44m   192.168.84.184   node-1   <none>           <none>
my-nginx-75d484d94b-l68gb   1/1     Running   0          3h44m   192.168.247.52   node-2   <none>           <none>
my-nginx-75d484d94b-zjgzv   1/1     Running   0          3h44m   192.168.247.53   node-2   <none>           <none>

vagrant@k8s-master:~$ kubectl exec -ti my-nginx-75d484d94b-f7rrk -- bash
root@my-nginx-75d484d94b-f7rrk:/# apt-get update && apt-get install -y stress
root@my-nginx-75d484d94b-f7rrk:/# stress --vm 1 --vm-bytes 504M
root@my-nginx-75d484d94b-f7rrk:/# exit
vagrant@k8s-master:~$ kubectl delete deployments. my-nginx
vagrant@k8s-master:~$ kubectl delete service nginx

```
---
---

# Criar NameSpaces 

https://kubernetes.io/docs/concepts/policy/limit-range/

```
vagrant@k8s-master:~$ kubectl get namespaces
NAME              STATUS   AGE
default           Active   12d
kube-node-lease   Active   12d
kube-public       Active   12d
kube-system       Active   12d

vagrant@k8s-master:~$ kubectl create namespace orbitex
namespace/orbitex created

vagrant@k8s-master:~$ kubectl get namespaces
NAME              STATUS   AGE
default           Active   12d
kube-node-lease   Active   12d
kube-public       Active   12d
kube-system       Active   12d
orbitex           Active   13s

vagrant@k8s-master:~$ kubectl describe namespace orbitex
Name:         orbitex
Labels:       <none>
Annotations:  <none>
Status:       Active

No resource quota.

No LimitRange resource.
vagrant@k8s-master:~$ kubectl get namespaces orbitex -o yaml
apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: "2020-02-18T14:08:08Z"
  name: orbitex
  resourceVersion: "770680"
  selfLink: /api/v1/namespaces/orbitex
  uid: 0254765b-d2a3-43bb-a17d-0397ca4e9a3b
spec:
  finalizers:
  - kubernetes
status:
  phase: Active

vagrant@k8s-master:~$ kubectl get namespaces orbitex -o yaml > my_first_namespace.yaml

vagrant@k8s-master:~$ vim my_first_namespace.yaml   

apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: "2020-02-18T14:08:08Z"
  name: orbitex
  resourceVersion: "770680"
  selfLink: /api/v1/namespaces/orbitex
  uid: 0254765b-d2a3-43bb-a17d-0397ca4e9a3b
spec:
  finalizers:
  - kubernetes
status:
  phase: Active

```
```
vagrant@k8s-master:~$ vim my_first_namespace.yaml 

apiVersion: v1
kind: Namespace
metadata:
  creationTimestamp: "2020-02-18T14:08:08Z"
  name: orbitex2
  resourceVersion: "770680"
  selfLink: /api/v1/namespaces/orbitex
  uid: 0254765b-d2a3-43bb-a17d-0397ca4e9a3b
spec:
  finalizers:
  - kubernetes
status:
  phase: Active

vagrant@k8s-master:~$ kubectl create -f  my_first_namespace.yaml 
namespace/orbitex2 created

vagrant@k8s-master:~$ kubectl get namespaces
NAME              STATUS   AGE
default           Active   12d
kube-node-lease   Active   12d
kube-public       Active   12d
kube-system       Active   12d
orbitex           Active   8m49s
orbitex2          Active   81s

```
---
---
# LimitRange

```
vagrant@k8s-master:~$ vim my_limited_namespace.yaml

apiVersion: v1
kind: LimitRange
metadata:
  name: limited-resources
spec:
  limits:
  - default:
      cpu: 1
      memory: 256Mi
    defaultRequest:
      cpu: 0.5
      memory: 128Mi
    type: Container

vagrant@k8s-master:~$ kubectl create -f my_limited_namespace.yaml -n orbitex
limitrange/limited-resources created


vagrant@k8s-master:~$ kubectl get limitranges -n orbitex
NAME                CREATED AT
limited-resources   2020-02-18T14:46:33Z

vagrant@k8s-master:~$ kubectl describe limitranges -n orbitex limited-resources
Name:       limited-resources
Namespace:  orbitex
Type        Resource  Min  Max  Default Request  Default Limit  Max Limit/Request Ratio
----        --------  ---  ---  ---------------  -------------  -----------------------
Container   cpu       -    -    500m             1              -
Container   memory    -    -    128Mi            256Mi          -

```

```
apiVersion: v1
kind: Pod
metadata:
  name: limited-pod
  namespace: orbitex2
spec:
  containers:
  - name: my-container
    image: nginx

```
`OU`

```
apiVersion: v1
kind: Pod
metadata:
  name: limited-pod
spec:
  containers:
  - name: my-container
    image: nginx
  
```
---
---
# Taint

```
vagrant@k8s-master:~$ kubectl get nodes
NAME         STATUS   ROLES    AGE   VERSION
k8s-master   Ready    master   12d   v1.17.2
node-1       Ready    <none>   12d   v1.17.2
node-2       Ready    <none>   12d   v1.17.2

vagrant@k8s-master:~$ kubectl describe node k8s-master

Name:               k8s-master
Roles:              master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=k8s-master
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/master=
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    projectcalico.org/IPv4Address: 172.16.1.10/24
                    projectcalico.org/IPv4IPIPTunnelAddr: 192.168.235.192
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Wed, 05 Feb 2020 17:23:12 +0000
Taints:             node-role.kubernetes.io/master:NoSchedule

```

`sem taint`

```
vagrant@k8s-master:~$ kubectl describe node node-1
Name:               node-1
Roles:              <none>
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=node-1
                    kubernetes.io/os=linux
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    projectcalico.org/IPv4Address: 172.16.1.11/24
                    projectcalico.org/IPv4IPIPTunnelAddr: 192.168.84.128
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Wed, 05 Feb 2020 17:26:57 +0000
Taints:             <none>

```

```
vagrant@k8s-master:~$ kubectl create -f deployment_limited.yaml
deployment.apps/my-nginx created

vagrant@k8s-master:~$ kubectl get deployments. --all-namespaces
NAMESPACE     NAME                      READY   UP-TO-DATE   AVAILABLE   AGE
default       my-nginx                  10/10   10           10          30s
kube-system   calico-kube-controllers   1/1     1            1           12d
kube-system   coredns                   2/2     2            2           12d

vagrant@k8s-master:~$ kubectl get pods -o wide
NAME                        READY   STATUS    RESTARTS   AGE   IP               NODE     NOMINATED NODE   READINESS GATES
my-nginx-75d484d94b-6jbx7   1/1     Running   0          74s   192.168.84.131   node-1   <none>           <none>
my-nginx-75d484d94b-968d5   1/1     Running   0          74s   192.168.84.130   node-1   <none>           <none>
my-nginx-75d484d94b-b4vz2   1/1     Running   0          74s   192.168.247.59   node-2   <none>           <none>
my-nginx-75d484d94b-clpnk   1/1     Running   0          74s   192.168.84.133   node-1   <none>           <none>
my-nginx-75d484d94b-dtpz2   1/1     Running   0          74s   192.168.84.190   node-1   <none>           <none>
my-nginx-75d484d94b-ks4l5   1/1     Running   0          74s   192.168.84.191   node-1   <none>           <none>
my-nginx-75d484d94b-lztkf   1/1     Running   0          74s   192.168.247.63   node-2   <none>           <none>
my-nginx-75d484d94b-mk5vd   1/1     Running   0          74s   192.168.247.60   node-2   <none>           <none>
my-nginx-75d484d94b-r4svl   1/1     Running   0          74s   192.168.247.61   node-2   <none>           <none>
my-nginx-75d484d94b-xcggs   1/1     Running   0          74s   192.168.247.62   node-2   <none>           <none>

vagrant@k8s-master:~$ kubectl scale --replicas=4 deployment my-nginx

vagrant@k8s-master:~$ kubectl get pods -o wide
NAME                        READY   STATUS    RESTARTS   AGE   IP               NODE     NOMINATED NODE   READINESS GATES
my-nginx-75d484d94b-b4vz2   1/1     Running   1          21h   192.168.247.2    node-2   <none>           <none>
my-nginx-75d484d94b-dtpz2   1/1     Running   1          21h   192.168.84.129   node-1   <none>           <none>
my-nginx-75d484d94b-ks4l5   1/1     Running   1          21h   192.168.84.132   node-1   <none>           <none>
my-nginx-75d484d94b-mk5vd   1/1     Running   1          21h   192.168.247.1    node-2   <none>           <none>
vagrant@k8s-master:~$ kubectl taint node node-1 key1=value1:NoSchedule
node/node-1 tainted

vagrant@k8s-master:~$ kubectl describe node node-1
Name:               node-1
Roles:              <none>
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=node-1
                    kubernetes.io/os=linux
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    projectcalico.org/IPv4Address: 172.16.1.11/24
                    projectcalico.org/IPv4IPIPTunnelAddr: 192.168.84.128
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Wed, 05 Feb 2020 17:26:57 +0000
Taints:             key1=value1:NoSchedule

vagrant@k8s-master:~$ kubectl get pods -o wide
NAME                        READY   STATUS    RESTARTS   AGE   IP               NODE     NOMINATED NODE   READINESS GATES
my-nginx-75d484d94b-7nxqk   1/1     Running   0          63s   192.168.247.8    node-2   <none>           <none>
my-nginx-75d484d94b-b4vz2   1/1     Running   1          22h   192.168.247.2    node-2   <none>           <none>
my-nginx-75d484d94b-dtpz2   1/1     Running   1          22h   192.168.84.129   node-1   <none>           <none>
my-nginx-75d484d94b-dtqvp   0/1     Pending   0          63s   <none>           <none>   <none>           <none>
my-nginx-75d484d94b-hfpzl   1/1     Running   0          63s   192.168.247.4    node-2   <none>           <none>
my-nginx-75d484d94b-ks4l5   1/1     Running   1          22h   192.168.84.132   node-1   <none>           <none>
my-nginx-75d484d94b-mk5vd   1/1     Running   1          22h   192.168.247.1    node-2   <none>           <none>
my-nginx-75d484d94b-n9bqv   1/1     Running   0          63s   192.168.247.5    node-2   <none>           <none>
my-nginx-75d484d94b-tfd9m   1/1     Running   0          63s   192.168.247.3    node-2   <none>           <none>
my-nginx-75d484d94b-vnz5q   1/1     Running   0          63s   192.168.247.6    node-2   <none>           <none>

vagrant@k8s-master:~$ vim deployment_limited.yaml 

`Obs:` Remover o resources, fica assim:

# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "2"
  creationTimestamp: "2020-02-18T15:04:06Z"
  generation: 4
  labels:
    run: nginx
  name: my-nginx
  namespace: default
  resourceVersion: "952860"
  selfLink: /apis/apps/v1/namespaces/default/deployments/my-nginx
  uid: fe8b4879-0e25-4c1e-9edc-6f9b253b9325
spec:
  progressDeadlineSeconds: 600
  replicas: 10
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      run: nginx
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        ports:
        - containerPort: 80        
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 10
  conditions:
  - lastTransitionTime: "2020-02-19T13:03:39Z"
    lastUpdateTime: "2020-02-19T13:03:39Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2020-02-18T15:04:06Z"
    lastUpdateTime: "2020-02-19T13:13:00Z"
    message: ReplicaSet "my-nginx-5578584966" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 4
  readyReplicas: 10
  replicas: 10
  updatedReplicas: 10

vagrant@k8s-master:~$ kubectl edit deployments. my-nginx
deployment.apps/my-nginx edited

vagrant@k8s-master:~$ kubectl get pod
NAME                        READY   STATUS              RESTARTS   AGE
my-nginx-5578584966-d8stt   0/1     ContainerCreating   0          8s
my-nginx-5578584966-hxqhz   0/1     ContainerCreating   0          2s
my-nginx-5578584966-k72rq   1/1     Running             0          8s
my-nginx-5578584966-mj57s   0/1     ContainerCreating   0          8s
my-nginx-5578584966-phzc2   0/1     ContainerCreating   0          8s
my-nginx-5578584966-vxbdw   0/1     ContainerCreating   0          8s
my-nginx-75d484d94b-7nxqk   0/1     Terminating         0          8m52s
my-nginx-75d484d94b-b4vz2   1/1     Running             1          22h
my-nginx-75d484d94b-dtpz2   1/1     Running             1          22h
my-nginx-75d484d94b-hfpzl   1/1     Running             0          8m52s
my-nginx-75d484d94b-ks4l5   1/1     Running             1          22h
my-nginx-75d484d94b-mk5vd   1/1     Running             1          22h
my-nginx-75d484d94b-n9bqv   1/1     Running             0          8m52s
my-nginx-75d484d94b-tfd9m   1/1     Running             0          8m52s
my-nginx-75d484d94b-vnz5q   0/1     Terminating         0          8m52s

vagrant@k8s-master:~$ kubectl get deployments
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   8/10    10           8           22h

vagrant@k8s-master:~$ kubectl get deployments
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   9/10    10           9           22h

vagrant@k8s-master:~$ kubectl get deployments
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   10/10   10           10          22h

```

* Resultado final

```
vagrant@k8s-master:~$ kubectl get pods -o wide
NAME                        READY   STATUS    RESTARTS   AGE     IP               NODE     NOMINATED NODE   READINESS GATES
my-nginx-5578584966-4pbtb   1/1     Running   0          4m59s   192.168.247.20   node-2   <none>           <none>
my-nginx-5578584966-d8stt   1/1     Running   0          5m17s   192.168.247.11   node-2   <none>           <none>
my-nginx-5578584966-hxqhz   1/1     Running   0          5m11s   192.168.247.15   node-2   <none>           <none>
my-nginx-5578584966-jj8tm   1/1     Running   0          5m8s    192.168.247.13   node-2   <none>           <none>
my-nginx-5578584966-jt99x   1/1     Running   0          5m1s    192.168.247.16   node-2   <none>           <none>
my-nginx-5578584966-k72rq   1/1     Running   0          5m17s   192.168.247.7    node-2   <none>           <none>
my-nginx-5578584966-mj57s   1/1     Running   0          5m17s   192.168.247.9    node-2   <none>           <none>
my-nginx-5578584966-phzc2   1/1     Running   0          5m17s   192.168.247.14   node-2   <none>           <none>
my-nginx-5578584966-svlv2   1/1     Running   0          5m5s    192.168.247.12   node-2   <none>           <none>
my-nginx-5578584966-vxbdw   1/1     Running   0          5m17s   192.168.247.10   node-2   <none>           <none>

```
* Removendo exemplo e reconstruindo e testando `Taint`:

```
vagrant@k8s-master:~$ kubectl delete deployments. my-nginx
deployment.apps "my-nginx" deleted

vagrant@k8s-master:~$ kubectl taint node node-1 key1=NoSchedule-
node/node-1 untainted


apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
  labels:
    run: nginx
  name: my-nginx
  namespace: default
spec:
  replicas: 10
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      terminationGracePeriodSeconds: 30


vagrant@k8s-master:~$ kubectl create -f deployment_no_limited.yaml
deployment.apps/my-nginx created

vagrant@k8s-master:~$ kubectl get pods -o wide
NAME                        READY   STATUS              RESTARTS   AGE   IP       NODE     NOMINATED NODE   READINESS GATES
my-nginx-5578584966-2fvt9   0/1     ContainerCreating   0          4s    <none>   node-1   <none>           <none>
my-nginx-5578584966-4s4bq   0/1     ContainerCreating   0          4s    <none>   node-2   <none>           <none>
my-nginx-5578584966-k5wjw   0/1     ContainerCreating   0          4s    <none>   node-1   <none>           <none>
my-nginx-5578584966-ld57k   0/1     ContainerCreating   0          4s    <none>   node-2   <none>           <none>

vagrant@k8s-master:~$ kubectl get deployments.
NAME       READY   UP-TO-DATE   AVAILABLE   AGE
my-nginx   4/4     4            4           40s

vagrant@k8s-master:~$ man hier
man: can't set the locale; make sure $LC_* and $LANG are correct

vagrant@k8s-master:~$ kubectl taint node node-1 key1=value1:NoSchedule
node/node-1 tainted

vagrant@k8s-master:~$ kubectl scale --replicas=10 deployment my-nginx
deployment.apps/my-nginx scaled

vagrant@k8s-master:~$ kubectl get pods -o wide

vagrant@k8s-master:~$ kubectl get pods -o wide
NAME                        READY   STATUS    RESTARTS   AGE     IP               NODE     NOMINATED NODE   READINESS GATES
my-nginx-5578584966-2fvt9   1/1     Running   0          6m19s   192.168.84.135   node-1   <none>           <none>
my-nginx-5578584966-4s4bq   1/1     Running   0          6m19s   192.168.247.25   node-2   <none>           <none>
my-nginx-5578584966-6czh6   1/1     Running   0          21s     192.168.247.23   node-2   <none>           <none>
my-nginx-5578584966-btdgk   1/1     Running   0          21s     192.168.247.31   node-2   <none>           <none>
my-nginx-5578584966-dqgmz   1/1     Running   0          21s     192.168.247.30   node-2   <none>           <none>
my-nginx-5578584966-k2wwc   1/1     Running   0          21s     192.168.247.24   node-2   <none>           <none>
my-nginx-5578584966-k5wjw   1/1     Running   0          6m19s   192.168.84.138   node-1   <none>           <none>
my-nginx-5578584966-ld57k   1/1     Running   0          6m19s   192.168.247.26   node-2   <none>           <none>
my-nginx-5578584966-vgsdc   1/1     Running   0          21s     192.168.247.28   node-2   <none>           <none>
my-nginx-5578584966-x5r4k   1/1     Running   0          21s     192.168.247.29   node-2   <none>           <none>

```
---
---

# Comando Taint

* Executar comando para os exeplos acima:

```
vagrant@k8s-master:~$ kubectl describe nodes node-1 | grep -i taint
Taints:             <none>

vagrant@k8s-master:~$ kubectl describe nodes  | grep -i taints
Taints:             node-role.kubernetes.io/master:NoSchedule
Taints:             <none>
Taints:             <none>

vagrant@k8s-master:~$ kubectl taint node k8s-master node-role.kubernetes.io/master:NoSchedule-
node/k8s-master untainted

vagrant@k8s-master:~$ kubectl describe nodes  | grep -i taints
Taints:             <none>
Taints:             <none>
Taints:             <none>

```

```
# kubectl taint node k8s-master node-role.kubernetes.io/master:NoSchedule
# kubectl taint node node-1 key1=value1:NoSchedule
# kubectl taint node node-1 key1=value1:NoSchedule-

# kubectl taint node node-2 key1=value1:NoSchedule
# kubectl taint node node-2 key1=value1:NoSchedule-

# kubectl taint node all key1=value1:NoExecute
# kubectl get pods -o wide

```
---
---

# Deployment

```
vagrant@k8s-master:~$ vim first-deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: nginx
    app: orbite
  name: first-deployment
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
        dc: Spanish
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx2
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30


vagrant@k8s-master:~$ kubectl create -f first-deployment.yaml
deployment.apps/first-deployment created

vagrant@k8s-master:~$ kubectl get deployments
NAME                  READY   UP-TO-DATE   AVAILABLE   AGE
first-deployment      1/1     1            1           5s

```

```
vagrant@k8s-master:~$ vim next-deployment.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: nginx
    app: orbite
  name: next-deployment
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
        dc: Brazil
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx2
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30


vagrant@k8s-master:~$ kubectl create -f next-deployment.yaml
deployment.apps/next-deployment created

vagrant@k8s-master:~$ kubectl get deployments
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
first-deployment   1/1     1            1           6m49s
next-deployment    1/1     1            1           82s

```

```
vagrant@k8s-master:~$ kubectl describe deployments first-deployment
Name:                   first-deployment
Namespace:              default
CreationTimestamp:      Wed, 19 Feb 2020 19:04:33 +0000
Labels:                 app=orbite
                        run=nginx
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               run=nginx
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  dc=Spanish
           run=nginx
  Containers:
   nginx2:
    Image:        nginx
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Progressing    True    NewReplicaSetAvailable
  Available      True    MinimumReplicasAvailable
OldReplicaSets:  <none>
NewReplicaSet:   first-deployment-55785965cc (1/1 replicas created)
Events:          <none>

vagrant@k8s-master:~$ kubectl get replicasets.
NAME                          DESIRED   CURRENT   READY   AGE
first-deployment-55785965cc   1         1         1       24h
next-deployment-7466846f79    1         1         1       24h

vagrant@k8s-master:~$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
first-deployment-55785965cc-76tsp   1/1     Running   1          24h
next-deployment-7466846f79-zmchn    1/1     Running   1          24h

vagrant@k8s-master:~$ kubectl describe pod first-deployment-55785965cc-76tsp
Name:         first-deployment-55785965cc-76tsp
Namespace:    default
Priority:     0
Node:         node-1/172.16.1.11
Start Time:   Wed, 19 Feb 2020 19:04:33 +0000
Labels:       dc=Spanish
              pod-template-hash=55785965cc
              run=nginx
Annotations:  cni.projectcalico.org/podIP: 192.168.84.145/32
Status:       Running
IP:           192.168.84.145
IPs:
  IP:           192.168.84.145
Controlled By:  ReplicaSet/first-deployment-55785965cc
Containers:
  nginx2:
    Container ID:   docker://3498718ab5791fe1b6f774a8ff05890e341433e8de15b65559abbb293a82450f
    Image:          nginx
    Image ID:       docker-pullable://nginx@sha256:ad5552c786f128e389a0263104ae39f3d3c7895579d45ae716f528185b36bc6f
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Thu, 20 Feb 2020 12:41:22 +0000
    Last State:     Terminated
      Reason:       Error
      Exit Code:    255
      Started:      Wed, 19 Feb 2020 19:04:37 +0000
      Finished:     Thu, 20 Feb 2020 12:40:30 +0000
    Ready:          True
    Restart Count:  1
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-t4hfb (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-t4hfb:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-t4hfb
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>

vagrant@k8s-master:~$ kubectl get pods -l dc=Spanish
NAME                                READY   STATUS    RESTARTS   AGE
first-deployment-55785965cc-76tsp   1/1     Running   1          24h

vagrant@k8s-master:~$ kubectl get pods -l dc=Brazil
NAME                               READY   STATUS    RESTARTS   AGE
next-deployment-7466846f79-zmchn   1/1     Running   1          24h

vagrant@k8s-master:~$ kubectl get pods -L dc
NAME                                READY   STATUS    RESTARTS   AGE   DC
first-deployment-55785965cc-76tsp   1/1     Running   1          24h   Spanish
next-deployment-7466846f79-zmchn    1/1     Running   1          24h   Brazil

vagrant@k8s-master:~$ kubectl get replicasets. -l dc=Brazil
NAME                         DESIRED   CURRENT   READY   AGE
next-deployment-7466846f79   1         1         1       24h

vagrant@k8s-master:~$ kubectl get replicasets. -l dc=Spanish
NAME                          DESIRED   CURRENT   READY   AGE
first-deployment-55785965cc   1         1         1       24h

vagrant@k8s-master:~$ kubectl get replicasets. -L dc
NAME                          DESIRED   CURRENT   READY   AGE   DC
first-deployment-55785965cc   1         1         1       24h   Spanish
next-deployment-7466846f79    1         1         1       24h   Brazil

```
---
---
# Label

```
vagrant@k8s-master:~$ kubectl describe nodes node-1
Name:               node-1
Roles:              <none>
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    disk=SSD-teste

vagrant@k8s-master:~$ kubectl describe nodes node-2
Name:               node-2
Roles:              <none>
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    disk=HDD-teste

vagrant@k8s-master:~$ kubectl label nodes node-1 --list
kubernetes.io/hostname=node-1
kubernetes.io/os=linux
beta.kubernetes.io/arch=amd64
beta.kubernetes.io/os=linux
disk=SSD-teste
kubernetes.io/arch=amd64

vagrant@k8s-master:~$ kubectl label nodes node-2 --list
kubernetes.io/hostname=node-2
kubernetes.io/os=linux
beta.kubernetes.io/arch=amd64
beta.kubernetes.io/os=linux
disk=HDD-teste
kubernetes.io/arch=amd64

vagrant@k8s-master:~$ kubectl label nodes k8s-master --list
beta.kubernetes.io/os=linux
kubernetes.io/arch=amd64
kubernetes.io/hostname=k8s-master
kubernetes.io/os=linux
node-role.kubernetes.io/master=
beta.kubernetes.io/arch=amd64

vagrant@k8s-master:~$ kubectl label nodes node-2 disk=HDD --overwrite
node/node-2 labeled

vagrant@k8s-master:~$ kubectl label nodes node-2 --list
kubernetes.io/arch=amd64
kubernetes.io/hostname=node-2
kubernetes.io/os=linux
beta.kubernetes.io/arch=amd64
beta.kubernetes.io/os=linux
disk=HDD

vagrant@k8s-master:~$ kubectl label nodes node-2 disk=HDD-teste2 --overwrite
node/node-2 labeled

vagrant@k8s-master:~$ kubectl label nodes node-2 --list
kubernetes.io/os=linux
beta.kubernetes.io/arch=amd64
beta.kubernetes.io/os=linux
disk=HDD-teste2
kubernetes.io/arch=amd64
kubernetes.io/hostname=node-2

vagrant@k8s-master:~$ kubectl label nodes node-2 dc=Brazil
node/node-2 labeled
vagrant@k8s-master:~$ kubectl label nodes node-1 dc=Spanish
node/node-1 labeled

```
```
vagrant@k8s-master:~$ cp first-deployment.yaml three-deployment.yaml

vagrant@k8s-master:~$ vim three-deployment.yaml 

apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: nginx
    app: orbite
  name: three-deployment
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
        dc: Mexico
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx2
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
      nodeSelector:
        disk: HDD-teste2

```

`Obs` Neste caso vamos criar o terceiro deploymente apenas aonde tiver disk HDD-teste2, se não exitir o disk ele não vai subir o pode e vai ficar em pending.

Conforme yaml:

```
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: nginx
    app: orbite
  name: three-deployment
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      run: nginx
  template:
    metadata:
      labels:
        run: nginx
        dc: Mexico
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx2
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
      nodeSelector:
        disk: HDD-teste2
        
```

```

vagrant@k8s-master:~$ kubectl create -f  three-deployment.yaml 
deployment.apps/three-deployment created

vagrant@k8s-master:~$ kubectl create -f three-deployment.yaml 
deployment.apps/three-deployment created

vagrant@k8s-master:~$ kubectl get deployments.
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
first-deployment   1/1     1            1           25h
next-deployment    1/1     1            1           25h
three-deployment   1/1     1            1           10s

vagrant@k8s-master:~$ kubectl get pods
NAME                                READY   STATUS    RESTARTS   AGE
first-deployment-55785965cc-76tsp   1/1     Running   1          25h
next-deployment-7466846f79-zmchn    1/1     Running   1          25h
three-deployment-565b9d6bd-8vkrt    1/1     Running   0          17s

vagrant@k8s-master:~$ kubectl label nodes node-1 --list
kubernetes.io/arch=amd64
kubernetes.io/hostname=node-1
kubernetes.io/os=linux
beta.kubernetes.io/arch=amd64
beta.kubernetes.io/os=linux
dc=Spanish
disk=SSD-teste

vagrant@k8s-master:~$ kubectl label nodes node-2 --list
kubernetes.io/os=linux
beta.kubernetes.io/arch=amd64
beta.kubernetes.io/os=linux
dc=Brazil
disk=HDD-teste2
kubernetes.io/arch=amd64
kubernetes.io/hostname=node-2

```
* Confirmando se criou no node-2 o pod

```
vagrant@k8s-master:~$ kubectl get pods -o wide
NAME                                READY   STATUS    RESTARTS   AGE     IP               NODE     NOMINATED NODE   READINESS GATES
first-deployment-55785965cc-76tsp   1/1     Running   1          25h     192.168.84.145   node-1   <none>           <none>
next-deployment-7466846f79-zmchn    1/1     Running   1          25h     192.168.247.35   node-2   <none>           <none>
three-deployment-565b9d6bd-8vkrt    1/1     Running   0          6m19s   192.168.247.36   node-2   <none>           <none>

```

* Editando e mudando de disk para node-1 para SSD-teste

```
vagrant@k8s-master:~$ kubectl edit deployments. three-deployment
deployment.apps/three-deployment edited


# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: "2020-02-20T20:22:29Z"
  generation: 1
  labels:
    app: orbite
    run: nginx
  name: three-deployment
  namespace: default
  resourceVersion: "1215304"
  selfLink: /apis/apps/v1/namespaces/default/deployments/three-deployment
  uid: 75394991-6531-427a-a1d0-123e961672d9
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      run: nginx
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      creationTimestamp: null
      labels:
        dc: Mexico
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx2
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      nodeSelector:
        disk: SSD-teste
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 1
  conditions:
  - lastTransitionTime: "2020-02-20T20:22:34Z"
    lastUpdateTime: "2020-02-20T20:22:34Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  - lastTransitionTime: "2020-02-20T20:22:29Z"
    lastUpdateTime: "2020-02-20T20:22:34Z"
    message: ReplicaSet "three-deployment-565b9d6bd" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  observedGeneration: 1
  readyReplicas: 1
  replicas: 1
  updatedReplicas: 1

vagrant@k8s-master:~$ kubectl get pods -o wide
NAME                                READY   STATUS    RESTARTS   AGE   IP               NODE     NOMINATED NODE   READINESS GATES
first-deployment-55785965cc-76tsp   1/1     Running   1          25h   192.168.84.145   node-1   <none>           <none>
next-deployment-7466846f79-zmchn    1/1     Running   1          25h   192.168.247.35   node-2   <none>           <none>
three-deployment-67b5675c9d-pjlh9   1/1     Running   0          26s   192.168.84.141   node-1   <none>           <none>

```

```
vagrant@k8s-master:~$ kubectl describe deployments. three-deployment
Name:                   three-deployment
Namespace:              default
CreationTimestamp:      Thu, 20 Feb 2020 20:22:29 +0000
Labels:                 app=orbite
                        run=nginx
Annotations:            deployment.kubernetes.io/revision: 2
Selector:               run=nginx
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  dc=Mexico
           run=nginx
  Containers:
   nginx2:
    Image:        nginx
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   three-deployment-67b5675c9d (1/1 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  13m   deployment-controller  Scaled up replica set three-deployment-565b9d6bd to 1
  Normal  ScalingReplicaSet  117s  deployment-controller  Scaled up replica set three-deployment-67b5675c9d to 1
  Normal  ScalingReplicaSet  113s  deployment-controller  Scaled down replica set three-deployment-565b9d6bd to 0

vagrant@k8s-master:~$ kubectl get replicaset
NAME                          DESIRED   CURRENT   READY   AGE
first-deployment-55785965cc   1         1         1       25h
next-deployment-7466846f79    1         1         1       25h
three-deployment-565b9d6bd    0         0         0       15m
three-deployment-67b5675c9d   1         1         1       3m43s


```
---
---

# Removendo dc e disk do Label

```
vagrant@k8s-master:~$ kubectl label nodes dc- --all
label "dc" not found.
node/k8s-master not labeled
node/node-1 labeled
node/node-2 labeled

vagrant@k8s-master:~$ kubectl label node-1 --list
error: the server doesn't have a resource type "node-1"

vagrant@k8s-master:~$ kubectl label nodes node-1 --list
beta.kubernetes.io/os=linux
disk=SSD-teste
kubernetes.io/arch=amd64
kubernetes.io/hostname=node-1
kubernetes.io/os=linux
beta.kubernetes.io/arch=amd64

vagrant@k8s-master:~$ kubectl label nodes disk- --all
label "disk" not found.
node/k8s-master not labeled
node/node-1 labeled
node/node-2 labeled

vagrant@k8s-master:~$ kubectl label nodes node-2 --list
beta.kubernetes.io/os=linux
kubernetes.io/arch=amd64
kubernetes.io/hostname=node-2
kubernetes.io/os=linux
beta.kubernetes.io/arch=amd64

vagrant@k8s-master:~$ kubectl label nodes node-1 --list
kubernetes.io/hostname=node-1
kubernetes.io/os=linux
beta.kubernetes.io/arch=amd64
beta.kubernetes.io/os=linux
kubernetes.io/arch=amd64

```
---
---

# Replicaset

```
vagrant@k8s-master:~$ vim first-replicaset.yaml

apiVersion: extensions/v1beta1
kind: ReplicaSet
metadata:
  name:  first-replicaset
spec:
  replicas: 3
  template:
    metadata:
      labels:
        system: orbitex
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80

```
* Caso pegar problemas com a versão da api seguir conforme comando da documentão e converter o yaml, copiar e inserir dentro do yaml:

```
vagrant@k8s-master:~$ kubectl convert -f ./first-replicaset.yaml --output-version apps/v1
kubectl convert is DEPRECATED and will be removed in a future version.
In order to convert, kubectl apply the object to the cluster, then kubectl get at the desired version.
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  creationTimestamp: null
  labels:
    system: orbitex
  name: first-replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      system: orbitex
  template:
    metadata:
      creationTimestamp: null
      labels:
        system: orbitex
    spec:
      containers:
      - image: nginx:1.7.9
        imagePullPolicy: IfNotPresent
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  replicas: 0

vagrant@k8s-master:~$ kubectl create -f first-replicaset.yaml
replicaset.apps/first-replicaset created

vagrant@k8s-master:~$ kubectl get replicaset
NAME                          DESIRED   CURRENT   READY   AGE
first-deployment-55785965cc   1         1         1       43h
first-replicaset              3         3         3       19s
next-deployment-7466846f79    1         1         1       43h
replica-set-primeiro          3         3         3       43m
three-deployment-565b9d6bd    0         0         0       18h
three-deployment-67b5675c9d   1         1         1       18h

vagrant@k8s-master:~$ kubectl describe replicaset first-replicaset
Name:         first-replicaset
Namespace:    default
Selector:     system=orbitex
Labels:       system=orbitex
Annotations:  <none>
Replicas:     3 current / 3 desired
Pods Status:  3 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  system=orbitex
  Containers:
   nginx:
    Image:        nginx:1.7.9
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age   From                   Message
  ----    ------            ----  ----                   -------
  Normal  SuccessfulCreate  3m2s  replicaset-controller  Created pod: first-replicaset-xldn2
  Normal  SuccessfulCreate  3m2s  replicaset-controller  Created pod: first-replicaset-79f84
  Normal  SuccessfulCreate  3m2s  replicaset-controller  Created pod: first-replicaset-h5qgk

vagrant@k8s-master:~$ kubectl get rs
NAME                          DESIRED   CURRENT   READY   AGE
first-deployment-55785965cc   1         1         1       44h
first-replicaset              3         3         3       9m46s
next-deployment-7466846f79    1         1         1       43h
replica-set-primeiro          3         3         3       53m
three-deployment-565b9d6bd    0         0         0       18h
three-deployment-67b5675c9d   1         1         1       18h

vagrant@k8s-master:~$ kubectl edit replicaset first-replicaset 
replicaset.apps/first-replicaset edited

vagrant@k8s-master:~$ kubectl get rs
NAME                          DESIRED   CURRENT   READY   AGE
first-deployment-55785965cc   1         1         1       44h
first-replicaset              4         4         4       12m
next-deployment-7466846f79    1         1         1       44h
replica-set-primeiro          3         3         3       55m
three-deployment-565b9d6bd    0         0         0       18h
three-deployment-67b5675c9d   1         1         1       18h

vagrant@k8s-master:~$ kubectl get pods -L system
NAME                                READY   STATUS    RESTARTS   AGE     SYSTEM
first-deployment-55785965cc-76tsp   1/1     Running   1          44h     
first-replicaset-79f84              1/1     Running   0          14m     orbitex
first-replicaset-h5qgk              1/1     Running   0          14m     orbitex
first-replicaset-llm6v              1/1     Running   0          2m34s   orbitex
first-replicaset-xldn2              1/1     Running   0          14m     orbitex
next-deployment-7466846f79-zmchn    1/1     Running   1          44h     
replica-set-primeiro-8hhr5          1/1     Running   0          57m     Giropops
replica-set-primeiro-ggh79          1/1     Running   0          57m     Giropops
replica-set-primeiro-kbff6          1/1     Running   0          57m     Giropops
three-deployment-67b5675c9d-pjlh9   1/1     Running   0          18h     

```
---
---

# Removendo os Labs

```
vagrant@k8s-master:~$ kubectl get replicaset
NAME                          DESIRED   CURRENT   READY   AGE
first-deployment-55785965cc   1         1         1       44h
first-replicaset              4         4         4       28m
next-deployment-7466846f79    1         1         1       44h
replica-set-primeiro          3         3         3       71m
three-deployment-565b9d6bd    0         0         0       19h
three-deployment-67b5675c9d   1         1         1       18h

vagrant@k8s-master:~$ kubectl delete -f first-replicaset.yaml
replicaset.apps "first-replicaset" deleted

vagrant@k8s-master:~$ kubectl delete -f primeiro-replicaset.yaml
replicaset.apps "replica-set-primeiro" deleted

vagrant@k8s-master:~$ kubectl get deployment
NAME               READY   UP-TO-DATE   AVAILABLE   AGE
first-deployment   1/1     1            1           44h
next-deployment    1/1     1            1           44h
three-deployment   1/1     1            1           19h

vagrant@k8s-master:~$ kubectl delete deployments -f first-deployment.yaml 
error: when paths, URLs, or stdin is provided as input, you may not specify resource arguments as well

vagrant@k8s-master:~$ kubectl delete  -f first-deployment.yaml 
deployment.apps "first-deployment" deleted

vagrant@k8s-master:~$ kubectl delete -f next-deployment.yaml 
deployment.apps "next-deployment" deleted

vagrant@k8s-master:~$ kubectl delete -f three-deployment.yaml 
deployment.apps "three-deployment" deleted

```
---
---

# Daemonset

```
vagrant@k8s-master:~$ vim 1-daemonset.yaml 

vagrant@k8s-master:~$ cat 1-daemonset.yaml 
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
  name: daemon-set-primeiro
spec:
  template:
    metadata:
      labels:
        system: Strigus
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80

vagrant@k8s-master:~$ kubectl convert -f ./1-daemonset.yaml --output-version apps/v1
kubectl convert is DEPRECATED and will be removed in a future version.
In order to convert, kubectl apply the object to the cluster, then kubectl get at the desired version.
apiVersion: apps/v1
kind: DaemonSet
metadata:
  annotations:
    deprecated.daemonset.template.generation: "0"
  creationTimestamp: null
  labels:
    system: Strigus
  name: daemon-set-primeiro
spec:
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      system: Strigus
  template:
    metadata:
      creationTimestamp: null
      labels:
        system: Strigus
    spec:
      containers:
      - image: nginx:1.7.9
        imagePullPolicy: IfNotPresent
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
  updateStrategy:
    type: OnDelete
status:
  currentNumberScheduled: 0
  desiredNumberScheduled: 0
  numberMisscheduled: 0
  numberReady: 0

vagrant@k8s-master:~$ kubectl create -f 1-daemonset.yaml
daemonset.apps/daemon-set-primeiro created

vagrant@k8s-master:~$ kubectl get daemonset
NAME                  DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemon-set-primeiro   3         3         3       3            3           <none>          2m41s

vagrant@k8s-master:~$ kubectl get pods
NAME                        READY   STATUS    RESTARTS   AGE
daemon-set-primeiro-8q6vv   1/1     Running   0          4m20s
daemon-set-primeiro-j2p66   1/1     Running   0          4m20s
daemon-set-primeiro-ms2vf   1/1     Running   0          4m20s

vagrant@k8s-master:~$ kubectl get pods -o wide
NAME                        READY   STATUS    RESTARTS   AGE     IP                NODE         NOMINATED NODE   READINESS GATES
daemon-set-primeiro-8q6vv   1/1     Running   0          4m34s   192.168.247.37    node-2       <none>           <none>
daemon-set-primeiro-j2p66   1/1     Running   0          4m34s   192.168.235.213   k8s-master   <none>           <none>
daemon-set-primeiro-ms2vf   1/1     Running   0          4m34s   192.168.84.147    node-1       <none>           <none>

```

`Obs` Caso seu node master estiver com taint pra no-scheduler siga o comando:

```
vagrant@k8s-master:~$ kubectl describe node k8s-master
Name:               k8s-master
Roles:              master
Labels:             beta.kubernetes.io/arch=amd64
                    beta.kubernetes.io/os=linux
                    kubernetes.io/arch=amd64
                    kubernetes.io/hostname=k8s-master
                    kubernetes.io/os=linux
                    node-role.kubernetes.io/master=
Annotations:        kubeadm.alpha.kubernetes.io/cri-socket: /var/run/dockershim.sock
                    node.alpha.kubernetes.io/ttl: 0
                    projectcalico.org/IPv4Address: 172.16.1.10/24
                    projectcalico.org/IPv4IPIPTunnelAddr: 192.168.235.192
                    volumes.kubernetes.io/controller-managed-attach-detach: true
CreationTimestamp:  Wed, 05 Feb 2020 17:23:12 +0000
Taints:             node-role.kubernetes.io/master:NoSchedule


vagrant@k8s-master:~$ kubectl taint node k8s-master node-role.kubernetes.io/master-
node/k8s-master untainted

```
* Voltando do taint da master como melhores práticas do mercado:

```
vagrant@k8s-master:~$ kubectl taint node k8s-master node-role.kubernetes.io/master=value1:NoSchedule 
node/k8s-master tainted

```
* Alterando via linha de comando sem usar o edit a versão do nginx do daemonset:

```
vagrant@k8s-master:~$ kubectl get daemonset
NAME                  DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemon-set-primeiro   2         2         2       2            2           <none>          16m
vagrant@k8s-master:~$ kubectl set image daemonset daemon-set-primeiro nginx=nginx:1:15.0
daemonset.apps/daemon-set-primeiro image updated

vagrant@k8s-master:~$ kubectl get ds
NAME                  DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
daemon-set-primeiro   2         2         2       0            2           <none>          21m

vagrant@k8s-master:~$ kubectl describe daemonset daemon-set-primeiro
Name:           daemon-set-primeiro
Selector:       system=Strigus
Node-Selector:  <none>
Labels:         system=Strigus
Annotations:    deprecated.daemonset.template.generation: 2
Desired Number of Nodes Scheduled: 2
Current Number of Nodes Scheduled: 2
Number of Nodes Scheduled with Up-to-date Pods: 0
Number of Nodes Scheduled with Available Pods: 2
Number of Nodes Misscheduled: 1
Pods Status:  3 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  system=Strigus
  Containers:
   nginx:
    Image:        nginx:1:15.0
    Port:         80/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age   From                  Message
  ----    ------            ----  ----                  -------
  Normal  SuccessfulCreate  21m   daemonset-controller  Created pod: daemon-set-primeiro-j2p66
  Normal  SuccessfulCreate  21m   daemonset-controller  Created pod: daemon-set-primeiro-8q6vv
  Normal  SuccessfulCreate  21m   daemonset-controller  Created pod: daemon-set-primeiro-ms2vf

vagrant@k8s-master:~$ kubectl get pod
NAME                        READY   STATUS    RESTARTS   AGE
daemon-set-primeiro-8q6vv   1/1     Running   0          23m
daemon-set-primeiro-j2p66   1/1     Running   0          23m
daemon-set-primeiro-ms2vf   1/1     Running   0          23m

vagrant@k8s-master:~$ kubectl describe pod daemon-set-primeiro-8q6vv
Name:         daemon-set-primeiro-8q6vv
Namespace:    default
Priority:     0
Node:         node-2/172.16.1.12
Start Time:   Fri, 21 Feb 2020 20:52:55 +0000
Labels:       controller-revision-hash=69bf64c84d
              pod-template-generation=1
              system=Strigus
Annotations:  cni.projectcalico.org/podIP: 192.168.247.37/32
Status:       Running
IP:           192.168.247.37
IPs:
  IP:           192.168.247.37
Controlled By:  DaemonSet/daemon-set-primeiro
Containers:
  nginx:
    Container ID:   docker://3ad575131d7efe2d762700f030a7063ad8b476bdbf19836a346e9cf4f5875359
    Image:          nginx:1.7.9
    Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
    Port:           80/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Fri, 21 Feb 2020 20:52:56 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-t4hfb (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-t4hfb:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-t4hfb
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/disk-pressure:NoSchedule
                 node.kubernetes.io/memory-pressure:NoSchedule
                 node.kubernetes.io/not-ready:NoExecute
                 node.kubernetes.io/pid-pressure:NoSchedule
                 node.kubernetes.io/unreachable:NoExecute
                 node.kubernetes.io/unschedulable:NoSchedule
Events:
  Type    Reason     Age   From               Message
  ----    ------     ----  ----               -------
  Normal  Scheduled  23m   default-scheduler  Successfully assigned default/daemon-set-primeiro-8q6vv to node-2
  Normal  Pulled     23m   kubelet, node-2    Container image "nginx:1.7.9" already present on machine
  Normal  Created    23m   kubelet, node-2    Created container nginx
  Normal  Started    23m   kubelet, node-2    Started container nginx

```
---
---

# Rollout

```
vagrant@k8s-master:~$ kubectl get daemonsets
NAME          DESIRED   CURRENT   READY   UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
1-daemonset   2         2         2       2            2           <none>          4d15h

vagrant@k8s-master:~$ kubectl rollout history daemonset 1-daemonset
daemonset.apps/1-daemonset 
REVISION  CHANGE-CAUSE
1         <none>

vagrant@k8s-master:~$ kubectl rollout history daemonset 1-daemonset --revision=1
daemonset.apps/1-daemonset with revision #1
Pod Template:
  Labels:	system=Strigus
  Containers:
   nginx:
    Image:	nginx:1.7.9
    Port:	80/TCP
    Host Port:	0/TCP
    Environment:	<none>
    Mounts:	<none>
  Volumes:	<none>

vagrant@k8s-master:~$ kubectl get pods
NAME                READY   STATUS    RESTARTS   AGE
1-daemonset-2lc6s   1/1     Running   1          4d15h
1-daemonset-p6xn2   1/1     Running   1          4d15h

vagrant@k8s-master:~$ kubectl describe pods 1-daemonset-2lc6s | grep -i nginx
  nginx:
    Image:          nginx:1.7.9
    Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451
  Normal  Pulled          4d15h              kubelet, node-2    Container image "nginx:1.7.9" already present on machine
  Normal  Created         4d15h              kubelet, node-2    Created container nginx
  Normal  Started         4d15h              kubelet, node-2    Started container nginx
  Normal  Pulled          11m                kubelet, node-2    Container image "nginx:1.7.9" already present on machine
  Normal  Created         11m                kubelet, node-2    Created container nginx
  Normal  Started         11m                kubelet, node-2    Started container nginx

vagrant@k8s-master:~$ kubectl rollout undo daemonset 1-daemonset --to-revision=1
daemonset.apps/1-daemonset skipped rollback (current template already matches revision 1)

vagrant@k8s-master:~$ kubectl describe pods 1-daemonset-2lc6s | grep Image
    Image:          nginx:1.7.9
    Image ID:       docker-pullable://nginx@sha256:e3456c851a152494c3e4ff5fcc26f240206abac0c9d794affb40e0714846c451


```
---
---

# Canary Deploy

```
vagrant@k8s-master:~$ git clone https://github.com/badtuxx/k8s-canary-deploy-example.git
Cloning into 'k8s-canary-deploy-example'...
remote: Enumerating objects: 159, done.
remote: Total 159 (delta 0), reused 0 (delta 0), pack-reused 159
Receiving objects: 100% (159/159), 1011.35 KiB | 1.37 MiB/s, done.
Resolving deltas: 100% (45/45), done.
Checking connectivity... done.

vagrant@k8s-master:~$ kubectl create -f app-v2.yml
error: unable to recognize "app-v1.yml": no matches for kind "Deployment" in version "extensions/v1beta1"

vagrant@k8s-master:~$ kubectl convert -f ./app-v2.yml --output-version apps/v1
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: giropops
    version: 1.0.0
  name: giropops-v1
spec:
  progressDeadlineSeconds: 2147483647
  replicas: 10
  revisionHistoryLimit: 2147483647
  selector:
    matchLabels:
      app: giropops
      version: 1.0.0
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
  template:
    metadata:
      annotations:
        prometheus.io/port: "32111"
        prometheus.io/scrape: "true"
      creationTimestamp: null
      labels:
        app: giropops
        version: 1.0.0
    spec:
      containers:
      - env:
        - name: VERSION
          value: 1.0.0
        image: linuxtips/nginx-prometheus-exporter:1.0.0
        imagePullPolicy: IfNotPresent
        name: giropops
        ports:
        - containerPort: 80
          protocol: TCP
        - containerPort: 32111
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status: {}

vagrant@k8s-master:~$ kubectl create -f app-v2.yml
deployment.apps/giropops-v1 created

vagrant@k8s-master:~$ kubectl get deployment
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
giropops-v1   10/10   10           10          2m13s

vagrant@k8s-master:~$ ls
1-daemonset.yaml         deployment_no_limited.yaml  k8s-canary-deploy-example  my_first_service_loadbalancer.yaml  pod-limeted.yaml          segundo-deployment.yaml
app-v1.yml               first-deployment.yaml       my_first_deployment.yaml   my_first_service_nodeport.yaml      primeiro-deployment.yaml  service-app.yml
app-v2.yml               first-replicaset.yaml       my_first_namespace.yaml    my_limited_namespace.yaml           primeiro-replicaset.yaml  terceiro-deployment.yaml
deployment_limited.yaml  first_deployment.yaml       my_first_service.yaml      next-deployment.yaml                quarto-deployment.yaml    three-deployment.yaml

vagrant@k8s-master:~$ vim service-app.yml 

vagrant@k8s-master:~$ kubectl create -f service-app.yml
service/giropops created

vagrant@k8s-master:~$  kubectl get services
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                        AGE
giropops     NodePort    10.102.140.106   <none>        80:32222/TCP,32111:32111/TCP   46s
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP                        20d

vagrant@k8s-master:~$ kubectl get pods -o wide
NAME                           READY   STATUS    RESTARTS   AGE     IP               NODE     NOMINATED NODE   READINESS GATES
giropops-v1-8597f74669-4hs5h   1/1     Running   0          8m42s   192.168.247.48   node-2   <none>           <none>
giropops-v1-8597f74669-5842q   1/1     Running   0          8m42s   192.168.84.156   node-1   <none>           <none>
giropops-v1-8597f74669-5tk8p   1/1     Running   0          8m42s   192.168.247.42   node-2   <none>           <none>
giropops-v1-8597f74669-bfsxc   1/1     Running   0          8m42s   192.168.247.43   node-2   <none>           <none>
giropops-v1-8597f74669-nj4hm   1/1     Running   0          8m42s   192.168.247.49   node-2   <none>           <none>
giropops-v1-8597f74669-qcp9z   1/1     Running   0          8m42s   192.168.84.155   node-1   <none>           <none>
giropops-v1-8597f74669-s5nlq   1/1     Running   0          8m42s   192.168.84.154   node-1   <none>           <none>
giropops-v1-8597f74669-t6gs7   1/1     Running   0          8m42s   192.168.84.148   node-1   <none>           <none>
giropops-v1-8597f74669-wdfhv   1/1     Running   0          8m42s   192.168.247.45   node-2   <none>           <none>
giropops-v1-8597f74669-xvv7w   1/1     Running   0          8m42s   192.168.84.153   node-1   <none>           <none>

vagrant@k8s-master:~$ cp k8s-canary-deploy-example/canary-deploy-app-v2-playbook/roles/common/files/* .

vagrant@k8s-master:~$ cp app-v2-canary.yml app-v3-canary.yml 

vagrant@k8s-master:~$ kubectl convert -f ./app-v3-canary.yml --output-version apps/v1

kubectl convert is DEPRECATED and will be removed in a future version.
In order to convert, kubectl apply the object to the cluster, then kubectl get at the desired version.
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: giropops
    version: 2.0.0
  name: giropops-v2
spec:
  progressDeadlineSeconds: 2147483647
  replicas: 1
  revisionHistoryLimit: 2147483647
  selector:
    matchLabels:
      app: giropops
      version: 2.0.0
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
  template:
    metadata:
      annotations:
        prometheus.io/port: "32111"
        prometheus.io/scrape: "true"
      creationTimestamp: null
      labels:
        app: giropops
        version: 2.0.0
    spec:
      containers:
      - env:
        - name: VERSION
          value: 2.0.0
        image: linuxtips/nginx-prometheus-exporter:2.0.0
        imagePullPolicy: IfNotPresent
        name: giropops
        ports:
        - containerPort: 80
          protocol: TCP
        - containerPort: 32111
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status: {}

vagrant@k8s-master:~$ kubectl create -f app-v3-canary.yml
deployment.apps/giropops-v2 created

vagrant@k8s-master:~$ kubectl get deploy
NAME          READY   UP-TO-DATE   AVAILABLE   AGE
giropops-v1   10/10   10           10          16m
giropops-v2   1/1     1            1           31s

vagrant@k8s-master:~$ kubectl create -f app-v2.yml
Error from server (AlreadyExists): error when creating "app-v2.yml": deployments.apps "giropops-v2" already exists

vagrant@k8s-master:~$ kubectl apply -f app-v2.yml
Warning: kubectl apply should be used on resource created by either kubectl create --save-config or kubectl apply
deployment.apps/giropops-v2 configured

vagrant@k8s-master:~$ kubectl get pods -o wide
NAME                           READY   STATUS              RESTARTS   AGE   IP               NODE     NOMINATED NODE   READINESS GATES
giropops-v1-8597f74669-4hs5h   1/1     Running             0          26m   192.168.247.48   node-2   <none>           <none>
giropops-v1-8597f74669-5842q   1/1     Running             0          26m   192.168.84.156   node-1   <none>           <none>
giropops-v1-8597f74669-5tk8p   1/1     Running             0          26m   192.168.247.42   node-2   <none>           <none>
giropops-v1-8597f74669-bfsxc   1/1     Running             0          26m   192.168.247.43   node-2   <none>           <none>
giropops-v1-8597f74669-nj4hm   1/1     Running             0          26m   192.168.247.49   node-2   <none>           <none>
giropops-v1-8597f74669-qcp9z   1/1     Running             0          26m   192.168.84.155   node-1   <none>           <none>
giropops-v1-8597f74669-s5nlq   1/1     Running             0          26m   192.168.84.154   node-1   <none>           <none>
giropops-v1-8597f74669-t6gs7   1/1     Running             0          26m   192.168.84.148   node-1   <none>           <none>
giropops-v1-8597f74669-wdfhv   1/1     Running             0          26m   192.168.247.45   node-2   <none>           <none>
giropops-v1-8597f74669-xvv7w   1/1     Running             0          26m   192.168.84.153   node-1   <none>           <none>
giropops-v2-6df4cbd48-4qtxp    1/1     Terminating         0          22s   192.168.84.157   node-1   <none>           <none>
giropops-v2-6df4cbd48-5x7kc    1/1     Running             0          10m   192.168.247.50   node-2   <none>           <none>
giropops-v2-6df4cbd48-79g2q    0/1     Terminating         0          22s   192.168.247.54   node-2   <none>           <none>
giropops-v2-6df4cbd48-csk6l    0/1     Terminating         0          22s   192.168.247.55   node-2   <none>           <none>
giropops-v2-6df4cbd48-nsj9g    1/1     Running             0          22s   192.168.247.47   node-2   <none>           <none>
giropops-v2-6df4cbd48-rbknr    1/1     Running             0          22s   192.168.84.160   node-1   <none>           <none>
giropops-v2-6df4cbd48-wd7jg    1/1     Running             0          22s   192.168.84.152   node-1   <none>           <none>
giropops-v2-7bb5867fb4-5mcn6   0/1     Running             0          3s    192.168.247.53   node-2   <none>           <none>
giropops-v2-7bb5867fb4-9cp2r   1/1     Running             0          15s   192.168.84.162   node-1   <none>           <none>
giropops-v2-7bb5867fb4-cshhq   1/1     Running             0          9s    192.168.247.52   node-2   <none>           <none>
giropops-v2-7bb5867fb4-hrq54   0/1     ContainerCreating   0          1s    <none>           node-1   <none>           <none>
giropops-v2-7bb5867fb4-jg428   1/1     Running             0          22s   192.168.247.56   node-2   <none>           <none>
giropops-v2-7bb5867fb4-qjxd4   1/1     Running             0          22s   192.168.84.158   node-1   <none>           <none>
giropops-v2-7bb5867fb4-wlwxr   1/1     Running             0          4s    192.168.84.166   node-1   <none>           <none>

vagrant@k8s-master:~$ kubectl scale deployment --replicas=3 giropops-v1
deployment.apps/giropops-v1 scaled

vagrant@k8s-master:~$ kubectl get pods -o wide
NAME                           READY   STATUS    RESTARTS   AGE     IP               NODE     NOMINATED NODE   READINESS GATES
giropops-v1-8597f74669-5tk8p   1/1     Running   0          29m     192.168.247.42   node-2   <none>           <none>
giropops-v1-8597f74669-bfsxc   1/1     Running   0          29m     192.168.247.43   node-2   <none>           <none>
giropops-v1-8597f74669-t6gs7   1/1     Running   0          29m     192.168.84.148   node-1   <none>           <none>
giropops-v2-7bb5867fb4-5mcn6   1/1     Running   0          2m42s   192.168.247.53   node-2   <none>           <none>
giropops-v2-7bb5867fb4-9cp2r   1/1     Running   0          2m54s   192.168.84.162   node-1   <none>           <none>
giropops-v2-7bb5867fb4-blct6   1/1     Running   0          2m28s   192.168.247.57   node-2   <none>           <none>
giropops-v2-7bb5867fb4-cshhq   1/1     Running   0          2m48s   192.168.247.52   node-2   <none>           <none>
giropops-v2-7bb5867fb4-hj5lm   1/1     Running   0          2m31s   192.168.84.163   node-1   <none>           <none>
giropops-v2-7bb5867fb4-hrq54   1/1     Running   0          2m40s   192.168.84.164   node-1   <none>           <none>
giropops-v2-7bb5867fb4-jg428   1/1     Running   0          3m1s    192.168.247.56   node-2   <none>           <none>
giropops-v2-7bb5867fb4-qjxd4   1/1     Running   0          3m1s    192.168.84.158   node-1   <none>           <none>
giropops-v2-7bb5867fb4-vtgcn   1/1     Running   0          2m36s   192.168.247.58   node-2   <none>           <none>
giropops-v2-7bb5867fb4-wlwxr   1/1     Running   0          2m43s   192.168.84.166   node-1   <none>           <none>

vagrant@k8s-master:~$ kubectl scale deployment --replicas=1 giropops-v1
deployment.apps/giropops-v1 scaled

vagrant@k8s-master:~$ kubectl delete deployments giropops-v1
deployment.apps "giropops-v1" deleted

vagrant@k8s-master:~$ kubectl rollout history deployment giropops-v2
deployment.apps/giropops-v2 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>

vagrant@k8s-master:~$ kubectl rollout history deployment giropops-v2 --revision=1
deployment.apps/giropops-v2 with revision #1
Pod Template:
  Labels:	app=giropops
	pod-template-hash=6df4cbd48
	version=2.0.0
  Annotations:	prometheus.io/port: 32111
	prometheus.io/scrape: true
  Containers:
   giropops:
    Image:	linuxtips/nginx-prometheus-exporter:2.0.0
    Ports:	80/TCP, 32111/TCP
    Host Ports:	0/TCP, 0/TCP
    Environment:
      VERSION:	2.0.0
    Mounts:	<none>
  Volumes:	<none>

vagrant@k8s-master:~$ kubectl rollout history deployment giropops-v2 --revision=2
deployment.apps/giropops-v2 with revision #2
Pod Template:
  Labels:	app=giropops
	pod-template-hash=7bb5867fb4
	version=2.0.0
  Annotations:	prometheus.io/port: 32111
	prometheus.io/scrape: true
  Containers:
   giropops:
    Image:	linuxtips/nginx-prometheus-exporter:2.0.0
    Ports:	80/TCP, 32111/TCP
    Host Ports:	0/TCP, 0/TCP
    Liveness:	http-get http://:80/ delay=0s timeout=1s period=10s #success=1 #failure=3
    Readiness:	http-get http://:80/ delay=0s timeout=1s period=10s #success=1 #failure=3
    Environment:
      VERSION:	2.0.0
    Mounts:	<none>
  Volumes:	<none>


```
---
---

# Rollouts e Rollbacks

```
vagrant@k8s-master:~$ kubectl describe deployments giropops-v2
Name:                   giropops-v2
Namespace:              default
CreationTimestamp:      Wed, 26 Feb 2020 14:08:04 +0000
Labels:                 app=giropops
                        version=2.0.0
Annotations:            deployment.kubernetes.io/revision: 2
                        kubectl.kubernetes.io/last-applied-configuration:
                          {"apiVersion":"apps/v1","kind":"Deployment","metadata":{"annotations":{},"creationTimestamp":null,"labels":{"app":"giropops","version":"2....
Selector:               app=giropops,version=2.0.0
Replicas:               10 desired | 10 updated | 10 total | 10 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  1 max unavailable, 1 max surge
Pod Template:
  Labels:       app=giropops
                version=2.0.0
  Annotations:  prometheus.io/port: 32111
                prometheus.io/scrape: true
  Containers:
   giropops:
    Image:       linuxtips/nginx-prometheus-exporter:2.0.0
    Ports:       80/TCP, 32111/TCP
    Host Ports:  0/TCP, 0/TCP
    Liveness:    http-get http://:80/ delay=0s timeout=1s period=10s #success=1 #failure=3
    Readiness:   http-get http://:80/ delay=0s timeout=1s period=10s #success=1 #failure=3
    Environment:
      VERSION:  2.0.0
    Mounts:     <none>
  Volumes:      <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
OldReplicaSets:  <none>
NewReplicaSet:   giropops-v2-7bb5867fb4 (10/10 replicas created)
Events:
  Type    Reason             Age                 From                   Message
  ----    ------             ----                ----                   -------
  Normal  ScalingReplicaSet  25m                 deployment-controller  Scaled up replica set giropops-v2-6df4cbd48 to 1
  Normal  ScalingReplicaSet  15m                 deployment-controller  Scaled up replica set giropops-v2-6df4cbd48 to 10
  Normal  ScalingReplicaSet  15m                 deployment-controller  Scaled up replica set giropops-v2-7bb5867fb4 to 1
  Normal  ScalingReplicaSet  15m                 deployment-controller  Scaled down replica set giropops-v2-6df4cbd48 to 9
  Normal  ScalingReplicaSet  15m                 deployment-controller  Scaled up replica set giropops-v2-7bb5867fb4 to 2
  Normal  ScalingReplicaSet  14m                 deployment-controller  Scaled down replica set giropops-v2-6df4cbd48 to 8
  Normal  ScalingReplicaSet  14m                 deployment-controller  Scaled up replica set giropops-v2-7bb5867fb4 to 3
  Normal  ScalingReplicaSet  14m                 deployment-controller  Scaled down replica set giropops-v2-6df4cbd48 to 7
  Normal  ScalingReplicaSet  14m                 deployment-controller  Scaled up replica set giropops-v2-7bb5867fb4 to 4
  Normal  ScalingReplicaSet  14m                 deployment-controller  Scaled down replica set giropops-v2-6df4cbd48 to 6
  Normal  ScalingReplicaSet  14m (x12 over 14m)  deployment-controller  (combined from similar events): Scaled down replica set giropops-v2-6df4cbd48 to 0

vagrant@k8s-master:~$ vim app-v2.yml

apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: giropops
    version: 2.0.0
  name: giropops-v2
spec:
  progressDeadlineSeconds: 2147483647
  replicas: 10
  revisionHistoryLimit: 2147483647
  selector:
    matchLabels:
      app: giropops
      version: 2.0.0
  strategy:
    rollingUpdate:
      maxSurge: 2
      maxUnavailable: 3
    type: RollingUpdate
  template:
    metadata:
      annotations:
        prometheus.io/port: "32111"
        prometheus.io/scrape: "true"
      creationTimestamp: null
      labels:
        app: giropops
        version: 2.0.0
    spec:
      containers:
      - env:
        - name: VERSION
          value: 2.0.0
        image: linuxtips/nginx-prometheus-exporter:2.0.0
        imagePullPolicy: IfNotPresent
        livenessProbe:
          failureThreshold: 3
          httpGet:
            path: /
            port: 80
            scheme: HTTP
          periodSeconds: 10
          successThreshold: 1
          timeoutSeconds: 1
        name: giropops
        ports:
        - containerPort: 80
          protocol: TCP
        - containerPort: 32111
          protocol: TCP
        readinessProbe:
          failureThreshold: 3
          httpGet:
            path: /
            port: 80
            scheme: HTTP
          periodSeconds: 10
          successThreshold: 1
          timeoutSeconds: 1
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status: {}


vagrant@k8s-master:~$ kubectl apply -f app-v2.yml
deployment.apps/giropops-v2 configured

vagrant@k8s-master:~$ kubectl delete -f app-v2.yml
deployment.apps "giropops-v2" deleted
vagrant@k8s-master:~$ kubectl get pods -o wide
NAME                           READY   STATUS        RESTARTS   AGE   IP               NODE     NOMINATED NODE   READINESS GATES
giropops-v2-7bb5867fb4-5mcn6   1/1     Terminating   0          23m   192.168.247.53   node-2   <none>           <none>
giropops-v2-7bb5867fb4-9cp2r   1/1     Terminating   0          23m   192.168.84.162   node-1   <none>           <none>
giropops-v2-7bb5867fb4-blct6   1/1     Terminating   0          23m   192.168.247.57   node-2   <none>           <none>
giropops-v2-7bb5867fb4-cshhq   1/1     Terminating   0          23m   192.168.247.52   node-2   <none>           <none>
giropops-v2-7bb5867fb4-hj5lm   1/1     Terminating   0          23m   192.168.84.163   node-1   <none>           <none>
giropops-v2-7bb5867fb4-hrq54   1/1     Terminating   0          23m   192.168.84.164   node-1   <none>           <none>
giropops-v2-7bb5867fb4-jg428   1/1     Terminating   0          23m   192.168.247.56   node-2   <none>           <none>
giropops-v2-7bb5867fb4-qjxd4   1/1     Terminating   0          23m   192.168.84.158   node-1   <none>           <none>
giropops-v2-7bb5867fb4-vtgcn   1/1     Terminating   0          23m   192.168.247.58   node-2   <none>           <none>
giropops-v2-7bb5867fb4-wlwxr   1/1     Terminating   0          23m   192.168.84.166   node-1   <none>           <none>

vagrant@k8s-master:~$ kubectl get pods -o wide
No resources found in default namespace.

vagrant@k8s-master:~$ kubectl create -f app-v2.yml
deployment.apps/giropops-v2 created

vagrant@k8s-master:~$ kubectl edit deployments giropops-v2

# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "1"
  creationTimestamp: "2020-02-26T14:43:31Z"
  generation: 1
  labels:
    app: giropops
    version: 1.0.0
  name: giropops-v2
  namespace: default
  resourceVersion: "1435722"
  selfLink: /apis/apps/v1/namespaces/default/deployments/giropops-v2
  uid: 002a9f8a-ff10-4bd6-a478-8048ec443eb9
spec:
  progressDeadlineSeconds: 2147483647
  replicas: 10
  revisionHistoryLimit: 2147483647
  selector:
    matchLabels:
      app: giropops
      version: 1.0.0
  strategy:
    rollingUpdate:
      maxSurge: 2
      maxUnavailable: 3
    type: RollingUpdate
  template:
    metadata:
      annotations:
        prometheus.io/port: "32111"
        prometheus.io/scrape: "true"
      creationTimestamp: null
      labels:
        app: giropops
        version: 1.0.0
    spec:
      containers:
      - env:
        - name: VERSION
          value: 1.0.0
        image: linuxtips/nginx-prometheus-exporter:1.0.0
        imagePullPolicy: IfNotPresent
        livenessProbe:
          failureThreshold: 3
          httpGet:
            path: /
            port: 80
            scheme: HTTP
          periodSeconds: 10
          successThreshold: 1
          timeoutSeconds: 1
        name: giropops
        ports:
        - containerPort: 80
          protocol: TCP
        - containerPort: 32111
          protocol: TCP
        readinessProbe:
          failureThreshold: 3
          httpGet:
            path: /
            port: 80
            scheme: HTTP
          periodSeconds: 10
          successThreshold: 1
          timeoutSeconds: 1
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 10
  conditions:
  - lastTransitionTime: "2020-02-26T14:43:37Z"
    lastUpdateTime: "2020-02-26T14:43:37Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  observedGeneration: 1
  readyReplicas: 10
  replicas: 10
  updatedReplicas: 10

vagrant@k8s-master:~$ kubectl rollout status deployment giropops-v2
deployment "giropops-v2" successfully rolled out

```
---
---
# Volume EmptyDir

```
vagrant@k8s-master:~$ vim pod-emptydir.yaml


apiVersion: v1
kind: Pod
metadata:
  name: busybox
  namespace: default
spec:
  containers:
  - image: busybox
    name: busy
    command:
      - sleep
      - "3600"
    volumeMounts:
    - mountPath: /giropops
      name: giropops-dir
  volumes:
  - name: giropops-dir
    emptyDir: {}

vagrant@k8s-master:~$ kubectl create -f pod-emptydir.yaml
pod/busybox created

vagrant@k8s-master:~$ kubectl get pods -o wide
NAME      READY   STATUS    RESTARTS   AGE    IP               NODE     NOMINATED NODE   READINESS GATES
busybox   1/1     Running   0          2m2s   192.168.84.173   node-1   <none>           <none>

vagrant@k8s-master:~$ kubectl describe pod busybox
Name:         busybox
Namespace:    default
Priority:     0
Node:         node-1/172.16.1.11
Start Time:   Wed, 26 Feb 2020 19:19:35 +0000
Labels:       <none>
Annotations:  cni.projectcalico.org/podIP: 192.168.84.173/32
Status:       Running
IP:           192.168.84.173
IPs:
  IP:  192.168.84.173
Containers:
  busy:
    Container ID:  docker://e3753716d3970a468c0efbc7b42af08eff1a911868b11a4083e285bdbad42caf
    Image:         busybox
    Image ID:      docker-pullable://busybox@sha256:6915be4043561d64e0ab0f8f098dc2ac48e077fe23f488ac24b665166898115a
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      3600
    State:          Running
      Started:      Wed, 26 Feb 2020 19:19:49 +0000
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /giropops from giropops-dir (rw)
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-t4hfb (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  giropops-dir:
    Type:       EmptyDir (a temporary directory that shares a pod's lifetime)
    Medium:     
    SizeLimit:  <unset>
  default-token-t4hfb:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-t4hfb
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age        From               Message
  ----    ------     ----       ----               -------
  Normal  Scheduled  <unknown>  default-scheduler  Successfully assigned default/busybox to node-1
  Normal  Pulling    2m44s      kubelet, node-1    Pulling image "busybox"
  Normal  Pulled     2m38s      kubelet, node-1    Successfully pulled image "busybox"
  Normal  Created    2m38s      kubelet, node-1    Created container busy
  Normal  Started    2m36s      kubelet, node-1    Started container busy

vagrant@k8s-master:~$  kubectl exec -ti busybox -- sh 
/ # ls
bin       dev       etc       giropops  home      proc      root      sys       tmp       usr       var
/ # pwd
/
/ # cd giropops/
/giropops # ls
/giropops # toutch teste1
sh: toutch: not found
/giropops # touch teste1
/giropops # ls
teste1
/giropops # exit

```
---
---

# Persistent Volume

https://kubernetes.io/docs/concepts/storage/persistent-volumes/

* PVC é uma declaração de necessidade de armazenamento que pode em algum momento tornar-se disponível - como em vinculado a alguns reais PV.

É um pouco como o conceito de programação assíncrona de um promessa. PVC promete que em algum momento"traduza" para o volume de armazenamento que seu aplicativo poderá usar e uma das características definidas como classe, tamanho e modo de acesso (ROX, RWO e RWX).

Esta é uma maneira de abstrair pensar sobre umimplementação de armazenamento particular longe de seus pods / implantações. Seu aplicativo na maioria dos casos não precisa declarar "me dê armazenamento NFS do servidor X do tamanho Y"; é mais como "Eu preciso de armazenamento persistente de classe padrão e tamanho Y".

Com isso, as implantações em diferentes clusters podemescolha diferentemente satisfazer essa necessidade. Pode-se vincular um dispositivo EBS, outro pode provisionar um GlusterFS, e seus principais manifestos ainda são os mesmos em ambos os casos.

Além disso, você pode ter modelos de solicitação de volumedefinidos em sua implantação, de modo que cada pod receba um PVC refletivo criado automaticamente (isto é, suportando a definição de armazenamento independente de infraestrutura para um grupo de pods escalonáveis ​​em que cada um precisa de seu próprio armazenamento dedicado.

* PVs são recursos no cluster. PVCs são solicitações para esses recursos e também atuam como verificações de declaração para o recurso.


 
Portanto, um volume persistente (PV) é o volume "físico" na máquina host que armazena seus dados persistentes. Uma reivindicação de volume persistente (PVC) é um pedido para a plataforma criar um PV para você e anexar PVs aos seus pods por meio de um PVC.

Algo parecido com

`Pod -> PVC -> PV -> Host machine`

```
vagrant@node-1:~$ sudo su -
root@node-1:~# cd /va

vagrant/ var/     
root@node-1:~# cd /va

vagrant/ var/     

root@node-1:~# cd /var/lib/kubelet/pods

root@node-1:/var/lib/kubelet/pods# ls
82ba2175-6554-46d0-b1db-94a21ffe8272  a13df6ec-5f9a-4537-b683-7bc3822283c1  db74a868-042b-4c8e-890b-fca9a03f8cd4

root@node-1:/var/lib/kubelet/pods# find . -iname "giropops-dir"
./82ba2175-6554-46d0-b1db-94a21ffe8272/plugins/kubernetes.io~empty-dir/giropops-dir
./82ba2175-6554-46d0-b1db-94a21ffe8272/volumes/kubernetes.io~empty-dir/giropops-dir

root@node-1:/var/lib/kubelet/pods# cd ./82ba2175-6554-46d0-b1db-94a21ffe8272/volumes/kubernetes.io~empty-dir/giropops-dir

root@node-1:/var/lib/kubelet/pods/82ba2175-6554-46d0-b1db-94a21ffe8272/volumes/kubernetes.io~empty-dir/giropops-dir# ls
teste1

root@node-1:/var/lib/kubelet/pods/82ba2175-6554-46d0-b1db-94a21ffe8272/volumes/kubernetes.io~empty-dir/giropops-dir# cd 

root@node-1:~# ls -lha /var/lib/kubelet/pods/82ba2175-6554-46d0-b1db-94a21ffe8272/volumes/kubernetes.io~empty-dir/giropops-dir
total 8.0K
drwxrwxrwx 2 root root 4.0K Feb 26 19:24 .
drwxr-xr-x 3 root root 4.0K Feb 26 19:19 ..
-rw-r--r-- 1 root root    0 Feb 26 19:24 teste1

vagrant@k8s-master:~$ kubectl delete -f pod-emptydir.yaml
pod "busybox" deleted

vagrant@k8s-master:~$ mkdir /opt/dados
mkdir: cannot create directory '/opt/dados': Permission denied

vagrant@k8s-master:~$ sudo mkdir /opt/dados

vagrant@k8s-master:~$ chmod 1777 /opt/dados/
chmod: changing permissions of '/opt/dados/': Operation not permitted

vagrant@k8s-master:~$ sudo chmod 1777 /opt/dados/

vagrant@k8s-master:~$ vim /etc/exports 

vagrant@k8s-master:~$ vim /etc/exports 

vagrant@k8s-master:~$ sudo su -

root@k8s-master:~# vim /etc/exports 

# /etc/exports: the access control list for filesystems which may be exported
#               to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#
/opt/dados *(rw,sync,no_root_squash,subtree_check)

root@k8s-master:~# exportfs -ar

```
`Obs:` * Colocar o ip do node master

```
vagrant@node-1:~$ showmount -e 172.16.1.10
Export list for 172.16.1.10:
/opt/dados *

vagrant@k8s-master:~$ kubectl convert -f ./primeiro-pv.yaml --output-version v1
kubectl convert is DEPRECATED and will be removed in a future version.
In order to convert, kubectl apply the object to the cluster, then kubectl get at the desired version.
apiVersion: v1
kind: PersistentVolume
metadata:
  creationTimestamp: null
  name: primeiro-pv
spec:
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 1Gi
  nfs:
    path: /opt/dados
    server: 172.16.1.10
  persistentVolumeReclaimPolicy: Retain
  volumeMode: Filesystem
status:
  phase: Pending

vagrant@k8s-master:~$ vim first-pv.yaml

apiVersion: v1
kind: PersistentVolume
metadata:
  creationTimestamp: null
  name: primeiro-pv
spec:
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 1Gi
  nfs:
    path: /opt/dados
    server: 172.16.1.10
  persistentVolumeReclaimPolicy: Retain
  volumeMode: Filesystem
status:
  phase: Pending

vagrant@k8s-master:~$ kubectl get pv
NAME          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   REASON   AGE
primeiro-pv   1Gi        RWX            Retain           Available                                   16h

vagrant@k8s-master:~$ kubectl describe pv primeiro-pv
Name:            primeiro-pv
Labels:          <none>
Annotations:     <none>
Finalizers:      [kubernetes.io/pv-protection]
StorageClass:    
Status:          Available
Claim:           
Reclaim Policy:  Retain
Access Modes:    RWX
VolumeMode:      Filesystem
Capacity:        1Gi
Node Affinity:   <none>
Message:         
Source:
    Type:      NFS (an NFS mount that lasts the lifetime of a pod)
    Server:    172.16.1.10
    Path:      /opt/dados
    ReadOnly:  false
Events:        <none>

vagrant@k8s-master:~$ vim primeiro-pvc.yaml

apiVersion: v1
kind : PersistentVolumeClaim
metadata:
  name: primeiro-pvc
spec:
  accessModes:
  - ReadWriteMany
  resources:
    requests:
      storage: 800Mi

vagrant@k8s-master:~$ kubectl create -f primeiro-pvc.yaml
persistentvolumeclaim/primeiro-pvc created

vagrant@k8s-master:~$ kubectl get pvc
NAME           STATUS   VOLUME        CAPACITY   ACCESS MODES   STORAGECLASS   AGE
primeiro-pvc   Bound    primeiro-pv   1Gi        RWX                           95s

vagrant@k8s-master:~$ kubectl get pv
NAME          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                  STORAGECLASS   REASON   AGE
primeiro-pv   1Gi        RWX            Retain           Bound    default/primeiro-pvc                           17h

vagrant@k8s-master:~$ kubectl describe pvc primeiro-pvc
Name:          primeiro-pvc
Namespace:     default
StorageClass:  
Status:        Bound
Volume:        primeiro-pv
Labels:        <none>
Annotations:   pv.kubernetes.io/bind-completed: yes
               pv.kubernetes.io/bound-by-controller: yes
Finalizers:    [kubernetes.io/pvc-protection]
Capacity:      1Gi
Access Modes:  RWX
VolumeMode:    Filesystem
Mounted By:    <none>
Events:        <none>

vagrant@k8s-master:~$ vim nfs-pv.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    run: nginx
  name: nginx
  namespace: default
spec:
  progressDeadlineSeconds: 600
  replicas: 1
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      run: nginx
  strategy:
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 1
    type: RollingUpdate
  template:
    metadata:
      labels:
        run: nginx
    spec:
      containers:
      - image: nginx
        imagePullPolicy: Always
        name: nginx
        volumeMounts:
        - name: nfs-pv
          mountPath: /giropops
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      volumes:
      - name: nfs-pv
        persistentVolumeClaim:
          claimName: primeiro-pvc
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30

vagrant@k8s-master:~$ kubectl create -f nfs-pv.yaml
deployment.apps/nginx created

vagrant@k8s-master:~$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
nginx-84844dfd49-qp96n   1/1     Running   0          114s

vagrant@k8s-master:~$ kubectl get deployments
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           2m1s

vagrant@k8s-master:~$ kubectl describe deployments. nginx
Name:                   nginx
Namespace:              default
CreationTimestamp:      Thu, 27 Feb 2020 14:29:41 +0000
Labels:                 run=nginx
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               run=nginx
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  1 max unavailable, 1 max surge
Pod Template:
  Labels:  run=nginx
  Containers:
   nginx:
    Image:        nginx
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:
      /giropops from nfs-pv (rw)
  Volumes:
   nfs-pv:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  primeiro-pvc
    ReadOnly:   false
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   nginx-84844dfd49 (1/1 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  2m53s  deployment-controller  Scaled up replica set nginx-84844dfd49 to 1

vagrant@k8s-master:~$ kubectl exec -ti nginx-84844dfd49-qp96n -- sh
# ls
bin  boot  dev	etc  giropops  home  lib  lib64  media	mnt  opt  proc	root  run  sbin  srv  sys  tmp	usr  var
# bash 			
root@nginx-84844dfd49-qp96n:/# cd giropops/
root@nginx-84844dfd49-qp96n:/giropops# touch teste11
root@nginx-84844dfd49-qp96n:/giropops# ls
teste11
root@nginx-84844dfd49-qp96n:/giropops# 

vagrant@k8s-master:~$ cd /opt/dados/

vagrant@k8s-master:/opt/dados$ ls
teste11

vagrant@k8s-master:/opt/dados$ kubectl get pods -o  wide
NAME                     READY   STATUS    RESTARTS   AGE     IP              NODE     NOMINATED NODE   READINESS GATES
nginx-84844dfd49-qp96n   1/1     Running   0          7m16s   192.168.247.1   node-2   <none>           <none>

```
---
---

# CronJobs

```
vagrant@k8s-master:/$ kubectl edit pv primeiro-pv
persistentvolume/primeiro-pv edited


# Please edit the object below. Lines beginning with a '#' will be ignored,
# and an empty file will abort the edit. If an error occurs while saving this file will be
# reopened with the relevant failures.
#
apiVersion: v1
kind: PersistentVolume
metadata:
  annotations:
    pv.kubernetes.io/bound-by-controller: "yes"
  creationTimestamp: "2020-02-26T20:51:50Z"
  finalizers:
  - kubernetes.io/pv-protection
  name: primeiro-pv
  resourceVersion: "1629878"
  selfLink: /api/v1/persistentvolumes/primeiro-pv
  uid: 3f7342f7-e00b-4741-b08d-04fbaaf5af3c
spec:
  accessModes:
  - ReadWriteMany
  capacity:
    storage: 1Gi
  claimRef:
    apiVersion: v1
    kind: PersistentVolumeClaim
    name: primeiro-pvc
    namespace: default
    resourceVersion: "1629876"
    uid: 82fd6947-a1dc-467d-8a2c-d75ceb1c98e8
  nfs:
    path: /opt/dados
    server: 172.16.1.10
  persistentVolumeReclaimPolicy: Delete
  volumeMode: Filesystem
status:
  phase: Bound

vagrant@k8s-master:/$ kubectl get pv
NAME          CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS   CLAIM                  STORAGECLASS   REASON   AGE
primeiro-pv   1Gi        RWX            Delete           Bound    default/primeiro-pvc                           21h

vagrant@k8s-master:~$ kubectl delete -f nfs-pv.yaml
deployment.apps "nginx" deleted

vagrant@k8s-master:~$ vim primeiro-cronjob.yaml

apiVersion: batch/v1beta1
kind: CronJob
metadata:
  name: giropops-cron
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: giropops-cron
            image: busybox
            args:
            - /bin/sh
            - -c
            - date; echo Bem Vindo ao Descomplicando Kubernetes - LinuxTips VAIIII :sleep 30
          restartPolicy: OnFailure

vagrant@k8s-master:~$ kubectl create -f primeiro-cronjob.yaml
cronjob.batch/giropops-cron created

vagrant@k8s-master:~$ kubectl get cronjobs.batch
NAME            SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
giropops-cron   */1 * * * *   False     0        42s             78s

vagrant@k8s-master:~$ kubectl describe cronjobs.batch giropops-cron
Name:                          giropops-cron
Namespace:                     default
Labels:                        <none>
Annotations:                   <none>
Schedule:                      */1 * * * *
Concurrency Policy:            Allow
Suspend:                       False
Successful Job History Limit:  3
Failed Job History Limit:      1
Starting Deadline Seconds:     <unset>
Selector:                      <unset>
Parallelism:                   <unset>
Completions:                   <unset>
Pod Template:
  Labels:  <none>
  Containers:
   giropops-cron:
    Image:      busybox
    Port:       <none>
    Host Port:  <none>
    Args:
      /bin/sh
      -c
      date; echo Bem Vindo ao Descomplicando Kubernetes - LinuxTips VAIIII :sleep 30
    Environment:     <none>
    Mounts:          <none>
  Volumes:           <none>
Last Schedule Time:  Thu, 27 Feb 2020 18:24:00 +0000
Active Jobs:         <none>
Events:
  Type    Reason            Age   From                Message
  ----    ------            ----  ----                -------
  Normal  SuccessfulCreate  110s  cronjob-controller  Created job giropops-cron-1582827780
  Normal  SawCompletedJob   100s  cronjob-controller  Saw completed job: giropops-cron-1582827780, status: Complete
  Normal  SuccessfulCreate  50s   cronjob-controller  Created job giropops-cron-1582827840
  Normal  SawCompletedJob   40s   cronjob-controller  Saw completed job: giropops-cron-1582827840, status: Complete

vagrant@k8s-master:~$ kubectl get jobs
NAME                       COMPLETIONS   DURATION   AGE
giropops-cron-1582827780   1/1           7s         2m46s
giropops-cron-1582827840   1/1           6s         106s
giropops-cron-1582827900   1/1           5s         45s

vagrant@k8s-master:~$ kubectl get jobs --watch
NAME                       COMPLETIONS   DURATION   AGE
giropops-cron-1582827840   1/1           6s         2m33s
giropops-cron-1582827900   1/1           5s         92s
giropops-cron-1582827960   1/1           5s         42s
giropops-cron-1582828020   0/1                      0s
giropops-cron-1582828020   0/1           0s         0s
giropops-cron-1582828020   1/1           5s         5s

vagrant@k8s-master:~$ kubectl get pods
NAME                             READY   STATUS      RESTARTS   AGE
giropops-cron-1582828200-fz6d9   0/1     Completed   0          2m16s
giropops-cron-1582828260-vsg65   0/1     Completed   0          76s
giropops-cron-1582828320-vz55z   0/1     Completed   0          16s

vagrant@k8s-master:~$ kubectl logs giropops-cron-1582828200-fz6d9
Thu Feb 27 18:30:05 UTC 2020
Bem Vindo ao Descomplicando Kubernetes - LinuxTips VAIIII :sleep 30

vagrant@k8s-master:~$ kubectl delete cronjobs.batch giropops-cron
cronjob.batch "giropops-cron" deleted

```
---
---
# Secrets

```
vagrant@k8s-master:~$ echo -n "orbitex " > secret.txt

vagrant@k8s-master:~$ cat secret.txt 
orbitex vagrant@k8s-master:~$ kubectl create secret generic my-secret --from-file=secret.txt
secret/my-secret created

vagrant@k8s-master:~$ kubectl get secret
NAME                  TYPE                                  DATA   AGE
default-token-t4hfb   kubernetes.io/service-account-token   3      22d
my-secret             Opaque                                1      8s

vagrant@k8s-master:~$ kubectl describe secrets my-secret
Name:         my-secret
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
secret.txt:  8 bytes

vagrant@k8s-master:~$ kubectl get secrets my-secret -o yaml
apiVersion: v1
data:
  secret.txt: b3JiaXRleCA=
kind: Secret
metadata:
  creationTimestamp: "2020-02-27T19:50:22Z"
  name: my-secret
  namespace: default
  resourceVersion: "1685844"
  selfLink: /api/v1/namespaces/default/secrets/my-secret
  uid: 75fabd86-dc6e-4a83-863c-0228ec4208c9
type: Opaque

agrant@k8s-master:~$ kubectl get secrets my-secret -o yaml
apiVersion: v1
data:
  secret.txt: b3JiaXRleCA=
kind: Secret
metadata:
  creationTimestamp: "2020-02-27T19:50:22Z"
  name: my-secret
  namespace: default
  resourceVersion: "1685844"
  selfLink: /api/v1/namespaces/default/secrets/my-secret
  uid: 75fabd86-dc6e-4a83-863c-0228ec4208c9
type: Opaque
vagrant@k8s-master:~$ echo 'b3JiaXRleCA' | base64 --decode
orbitex base64: invalid input

vagrant@k8s-master:~$ echo "b3JiaXRleCA=" | base64 --decode
orbitex vagrant@k8s-master:~$ 

vagrant@k8s-master:~$ vim pode-secret.yaml

apiVersion: v1
kind: Pod
metadata:
  name: teste-secret
  namespace: default
spec:
  containers:
  - image: busybox
    name: busy
    command:
      - sleep
      - "3600"
    volumeMounts:
    - mountPath: /tmp/giropops
      name: my-volume-secret
  volumes:
  - name: my-volume-secret
    secret:
      secretName: my-secret

vagrant@k8s-master:~$ kubectl create -f pode-secret.yaml
pod/teste-secret created

vagrant@k8s-master:~$ kubectl get pods
NAME           READY   STATUS    RESTARTS   AGE
teste-secret   1/1     Running   0          43s

vagrant@k8s-master:~$ kubectl exec -ti teste-secret -- sh
/ # ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var
/ # cd tmp/giropops/
/tmp/giropops # ls
secret.txt
/tmp/giropops # cat secret.txt 
orbitex /tmp/giropops # 

vagrant@k8s-master:~$ kubectl delete pods teste-secret
pod "teste-secret" deleted

vagrant@k8s-master:~$ kubectl create secret generic my-literal-secret --from-literal user=orbitex --from-literal password=arthur
secret/my-literal-secret created

vagrant@k8s-master:~$ kubectl get secret
NAME                  TYPE                                  DATA   AGE
default-token-t4hfb   kubernetes.io/service-account-token   3      22d
my-literal-secret     Opaque                                2      23s
my-secret             Opaque                                1      29m

vagrant@k8s-master:~$ kubectl describe secret my-literal-secret
Name:         my-literal-secret
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
password:  6 bytes
user:      7 bytes

vagrant@k8s-master:~$ kubectl describe secrets my-literal-secret
Name:         my-literal-secret
Namespace:    default
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
password:  6 bytes
user:      7 bytes

vagrant@k8s-master:~$ kubectl get secret my-literal-secret -o yaml
apiVersion: v1
data:
  password: YXJ0aHVy
  user: b3JiaXRleA==
kind: Secret
metadata:
  creationTimestamp: "2020-02-27T20:19:15Z"
  name: my-literal-secret
  namespace: default
  resourceVersion: "1690041"
  selfLink: /api/v1/namespaces/default/secrets/my-literal-secret
  uid: a4c8656a-db25-49d4-8903-b3fb81834f27
type: Opaque

vagrant@k8s-master:~$ echo 'YXJ0aHVy' | base64 --decode
arthurvagrant@k8s-master:~$ echo 'b3JiaXRleA==' | base64 --decode
orbitexvagrant@k8s-master:~$ 

vagrant@k8s-master:~$ kubectl create -f pod-secret-env.yaml
pod/teste-secret-env created

vagrant@k8s-master:~$ kubectl get pods
NAME               READY   STATUS    RESTARTS   AGE
teste-secret-env   1/1     Running   0          22s

vagrant@k8s-master:~$ kubectl exec -ti teste-secret-env -- sh
/ # ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var
/ # set
GIROPOPS_PORT='tcp://10.102.140.106:80'
GIROPOPS_PORT_32111_TCP='tcp://10.102.140.106:32111'
GIROPOPS_PORT_32111_TCP_ADDR='10.102.140.106'
GIROPOPS_PORT_32111_TCP_PORT='32111'
GIROPOPS_PORT_32111_TCP_PROTO='tcp'
GIROPOPS_PORT_80_TCP='tcp://10.102.140.106:80'
GIROPOPS_PORT_80_TCP_ADDR='10.102.140.106'
GIROPOPS_PORT_80_TCP_PORT='80'
GIROPOPS_PORT_80_TCP_PROTO='tcp'
GIROPOPS_SERVICE_HOST='10.102.140.106'
GIROPOPS_SERVICE_PORT='80'
GIROPOPS_SERVICE_PORT_HTTP='80'
GIROPOPS_SERVICE_PORT_PROMETHEUS='32111'
HISTFILE='/root/.ash_history'
HOME='/root'
HOSTNAME='teste-secret-env'
IFS=' 	
'
KUBERNETES_PORT='tcp://10.96.0.1:443'
KUBERNETES_PORT_443_TCP='tcp://10.96.0.1:443'
KUBERNETES_PORT_443_TCP_ADDR='10.96.0.1'
KUBERNETES_PORT_443_TCP_PORT='443'
KUBERNETES_PORT_443_TCP_PROTO='tcp'
KUBERNETES_SERVICE_HOST='10.96.0.1'
KUBERNETES_SERVICE_PORT='443'
KUBERNETES_SERVICE_PORT_HTTPS='443'
LINENO=''
MEU_PASSWORD='arthur'
MEU_USERNAME='orbitex'
OPTIND='1'
PATH='/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin'
PPID='0'
PS1='\w \$ '
PS2='> '
PS4='+ '
PWD='/'
SHLVL='1'
TERM='xterm'
_='ls'
/ # 

```
---
---
# Configmaps

```
vagrant@k8s-master:~$ kubectl delete pods teste-secret-env
pod "teste-secret-env" deleted

vagrant@k8s-master:~$ echo amarela > frutas/banana
vagrant@k8s-master:~$ echo vermelho > frutas/morango
vagrant@k8s-master:~$ echo verde > frutas/limao
vagrant@k8s-master:~$ echo "verde e vermelho" > frutas/melancia
vagrant@k8s-master:~$ echo kiwi > predileta
vagrant@k8s-master:~$ ls frutas
banana  limao  melancia  morango

vagrant@k8s-master:~$ ls predileta
predileta

vagrant@k8s-master:~$ kubectl create configmap cores-frutas --from-literal uva=roxa --from-file=predileta --from-file=frutas/
configmap/cores-frutas created

vagrant@k8s-master:~$ kubectl get configmap
NAME           DATA   AGE
cores-frutas   6      43s

vagrant@k8s-master:~$ kubectl describe configmap
Name:         cores-frutas
Namespace:    default
Labels:       <none>
Annotations:  <none>

Data
====
uva:
----
roxa
banana:
----
amarela

limao:
----
verde

melancia:
----
verde e vermelho

morango:
----
vermelho

predileta:
----
kiwi

Events:  <none>

vagrant@k8s-master:~$ vim pod-configmap.yaml

apiVersion: v1
kind: Pod
metadata:
  name: busybox-configmap
  namespace: default
spec:
  containers:
  - image: busybox
    name: busy-configmap
    command:
      - sleep
      - "3600"
    env:
    - name: frutas
      valueFrom:
        configMapKeyRef:
          name: cores-frutas
          key: predileta

vagrant@k8s-master:~$ kubectl create -f pod-configmap.yaml
pod/busybox-configmap created

vagrant@k8s-master:~$ kubectl get pods
NAME                READY   STATUS    RESTARTS   AGE
busybox-configmap   1/1     Running   0          9m28s
vagrant@k8s-master:~$ kubectl describe pods busybox-configmap
Name:         busybox-configmap
Namespace:    default
Priority:     0
Node:         node-1/172.16.1.11
Start Time:   Fri, 28 Feb 2020 13:42:41 +0000
Labels:       <none>
Annotations:  cni.projectcalico.org/podIP: 192.168.84.177/32
Status:       Running
IP:           192.168.84.177
IPs:
  IP:  192.168.84.177
Containers:
  busy-configmap:
    Container ID:  docker://5ff5f46350eac49537f49cc370d76304d9eeffeca6051eca533dfd6fb801e0cb
    Image:         busybox
    Image ID:      docker-pullable://busybox@sha256:6915be4043561d64e0ab0f8f098dc2ac48e077fe23f488ac24b665166898115a
    Port:          <none>
    Host Port:     <none>
    Command:
      sleep
      3600
    State:          Running
      Started:      Fri, 28 Feb 2020 13:42:45 +0000
    Ready:          True
    Restart Count:  0
    Environment:
      frutas:  <set to the key 'predileta' of config map 'cores-frutas'>  Optional: false
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-t4hfb (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-t4hfb:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-t4hfb
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age        From               Message
  ----    ------     ----       ----               -------
  Normal  Scheduled  <unknown>  default-scheduler  Successfully assigned default/busybox-configmap to node-1
  Normal  Pulling    9m54s      kubelet, node-1    Pulling image "busybox"
  Normal  Pulled     9m52s      kubelet, node-1    Successfully pulled image "busybox"
  Normal  Created    9m51s      kubelet, node-1    Created container busy-configmap
  Normal  Started    9m51s      kubelet, node-1    Started container busy-configmap

vagrant@k8s-master:~$ kubectl exec -ti busybox-configmap -- sh
/ # ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var
/ # set
GIROPOPS_PORT='tcp://10.102.140.106:80'
GIROPOPS_PORT_32111_TCP='tcp://10.102.140.106:32111'
GIROPOPS_PORT_32111_TCP_ADDR='10.102.140.106'
GIROPOPS_PORT_32111_TCP_PORT='32111'
GIROPOPS_PORT_32111_TCP_PROTO='tcp'
GIROPOPS_PORT_80_TCP='tcp://10.102.140.106:80'
GIROPOPS_PORT_80_TCP_ADDR='10.102.140.106'
GIROPOPS_PORT_80_TCP_PORT='80'
GIROPOPS_PORT_80_TCP_PROTO='tcp'
GIROPOPS_SERVICE_HOST='10.102.140.106'
GIROPOPS_SERVICE_PORT='80'
GIROPOPS_SERVICE_PORT_HTTP='80'
GIROPOPS_SERVICE_PORT_PROMETHEUS='32111'
HISTFILE='/root/.ash_history'
HOME='/root'
HOSTNAME='busybox-configmap'
IFS=' 	
'
KUBERNETES_PORT='tcp://10.96.0.1:443'
KUBERNETES_PORT_443_TCP='tcp://10.96.0.1:443'
KUBERNETES_PORT_443_TCP_ADDR='10.96.0.1'
KUBERNETES_PORT_443_TCP_PORT='443'
KUBERNETES_PORT_443_TCP_PROTO='tcp'
KUBERNETES_SERVICE_HOST='10.96.0.1'
KUBERNETES_SERVICE_PORT='443'
KUBERNETES_SERVICE_PORT_HTTPS='443'
LINENO=''
OPTIND='1'
PATH='/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin'
PPID='0'
PS1='\w \$ '
PS2='> '
PS4='+ '
PWD='/'
SHLVL='1'
TERM='xterm'
_='ls'
frutas='kiwi
'
/ # 

vagrant@k8s-master:~$ kubectl delete -f pod-configmap.yaml
pod "busybox-configmap" deleted

vagrant@k8s-master:~$ vim pod-configmap.yaml

apiVersion: v1
kind: Pod
metadata:
  name: busybox-configmap
  namespace: default
spec:
  containers:
  - image: busybox
    name: busy-configmap
    command:
      - sleep
      - "3600"
    envFrom:
    - configMapRef:
        name: cores-frutas

vagrant@k8s-master:~$ kubectl create -f pod-configmap.yaml
pod/busybox-configmap created

vagrant@k8s-master:~$ kubectl get pods
NAME                READY   STATUS    RESTARTS   AGE
busybox-configmap   1/1     Running   0          8s

vagrant@k8s-master:~$ kubectl exec -ti busybox-configmap -- sh
/ # ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var
/ # set
GIROPOPS_PORT='tcp://10.102.140.106:80'
GIROPOPS_PORT_32111_TCP='tcp://10.102.140.106:32111'
GIROPOPS_PORT_32111_TCP_ADDR='10.102.140.106'
GIROPOPS_PORT_32111_TCP_PORT='32111'
GIROPOPS_PORT_32111_TCP_PROTO='tcp'
GIROPOPS_PORT_80_TCP='tcp://10.102.140.106:80'
GIROPOPS_PORT_80_TCP_ADDR='10.102.140.106'
GIROPOPS_PORT_80_TCP_PORT='80'
GIROPOPS_PORT_80_TCP_PROTO='tcp'
GIROPOPS_SERVICE_HOST='10.102.140.106'
GIROPOPS_SERVICE_PORT='80'
GIROPOPS_SERVICE_PORT_HTTP='80'
GIROPOPS_SERVICE_PORT_PROMETHEUS='32111'
HISTFILE='/root/.ash_history'
HOME='/root'
HOSTNAME='busybox-configmap'
IFS=' 	
'
KUBERNETES_PORT='tcp://10.96.0.1:443'
KUBERNETES_PORT_443_TCP='tcp://10.96.0.1:443'
KUBERNETES_PORT_443_TCP_ADDR='10.96.0.1'
KUBERNETES_PORT_443_TCP_PORT='443'
KUBERNETES_PORT_443_TCP_PROTO='tcp'
KUBERNETES_SERVICE_HOST='10.96.0.1'
KUBERNETES_SERVICE_PORT='443'
KUBERNETES_SERVICE_PORT_HTTPS='443'
LINENO=''
OPTIND='1'
PATH='/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin'
PPID='0'
PS1='\w \$ '
PS2='> '
PS4='+ '
PWD='/'
SHLVL='1'
TERM='xterm'
_='ls'
banana='amarela
'
limao='verde
'
melancia='verde e vermelho
'
morango='vermelho
'
predileta='kiwi
'
uva='roxa'
/ # 

vagrant@k8s-master:~$ vim pod-configmap-file.yaml

piVersion: v1
kind: Pod
metadata:
  name: busybox-configmap-file
  namespace: default
spec:
  containers:
  - image: busybox
    name: busy-configmap
    command:
      - sleep
      - "3600"
    volumeMounts:
    - name: meu-configmap-vol
      mountPath: /etc/frutas
  volumes:
  - name: meu-configmap-vol
    configMap:
      name: cores-frutas


vagrant@k8s-master:~$ kubectl create -f pod-configmap-file.yaml
pod/busybox-configmap-file created

vagrant@k8s-master:~$ kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
busybox-configmap-file   1/1     Running   0          44s

vagrant@k8s-master:~$ kubectl exec -ti busybox-configmap-file -- sh
/ # ls
bin   dev   etc   home  proc  root  sys   tmp   usr   var
/ # cd /etc/frutas/
/etc/frutas # ls
banana     limao      melancia   morango    predileta  uva
/etc/frutas # 
/etc/frutas # ls -lha
total 12K    
drwxrwxrwx    3 root     root        4.0K Feb 28 15:30 .
drwxr-xr-x    1 root     root        4.0K Feb 28 15:30 ..
drwxr-xr-x    2 root     root        4.0K Feb 28 15:30 ..2020_02_28_15_30_08.338479915
lrwxrwxrwx    1 root     root          31 Feb 28 15:30 ..data -> ..2020_02_28_15_30_08.338479915
lrwxrwxrwx    1 root     root          13 Feb 28 15:30 banana -> ..data/banana
lrwxrwxrwx    1 root     root          12 Feb 28 15:30 limao -> ..data/limao
lrwxrwxrwx    1 root     root          15 Feb 28 15:30 melancia -> ..data/melancia
lrwxrwxrwx    1 root     root          14 Feb 28 15:30 morango -> ..data/morango
lrwxrwxrwx    1 root     root          16 Feb 28 15:30 predileta -> ..data/predileta
lrwxrwxrwx    1 root     root          10 Feb 28 15:30 uva -> ..data/uva
/etc/frutas # 

```
---
---

# Initcontainer

```
vagrant@k8s-master:~$ vim nginx-initcontainer.yaml 

apiVersion: v1
kind: Pod
metadata:
  name: init-demo
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
    volumeMounts:
    - name: workdir
      mountPath: /usr/share/nginx/html
  initContainers:
  - name: install
    image: busybox
    command:
    - wget
    - "-O"
    - "/work-dir/index.html"
    - http://kubernetes.io
    volumeMounts:
    - name: workdir
      mountPath: "/work-dir"
  dnsPolicy: Default
  volumes:
  - name: workdir
    emptyDir: {}

vagrant@k8s-master:~$ kubectl create -f nginx-initcontainer.yaml
pod/init-demo created

vagrant@k8s-master:~$ kubectl get pods
NAME        READY   STATUS    RESTARTS   AGE
init-demo   1/1     Running   0          2m39s

vagrant@k8s-master:~$ kubectl exec -ti init-demo -- sh
# ls
bin  boot  dev	etc  home  lib	lib64  media  mnt  opt	proc  root  run  sbin  srv  sys  tmp  usr  var
# cd /usr/share	
# ls
X11	    base-passwd      common-licenses  dict	dpkg	    gcc-8  java      lintian  menu   pam	  pixmaps   sensible-utils  xml
adduser     bash-completion  debconf	      doc	fontconfig  gdb    keyrings  locale   misc   pam-configs  polkit-1  tabset	    zoneinfo
base-files  bug		     debianutils      doc-base	fonts	    info   libc-bin  man      nginx  perl5	  readline  terminfo
# cd nginx
# ls
html
# cd html
# ls
index.html
# cat index.html

<!doctype html><html id=home lang=en><head><meta name=generator content="Hugo 0.59.1"><script async src="https://www.googletagmanager.com/gtag/js?id=UA-36037335-10"></script><script>window.dataLayer=window.dataLayer||[];function gtag(){dataLayer.push(arguments);}
gtag('js',new Date());gtag('config','UA-36037335-10');</script><meta charset=utf-8><title>Production-Grade Container Orchestration - Kubernetes</title><meta name=viewport content="width=device-width,initial-scale=1"><meta name=theme-color content="#326ce5"><link rel="shortcut icon" type=image/png href=/images/favicon.png><link rel=stylesheet href=/css/style.e78f6b6cb395bf1ebff750caf8b497ce6f23c485f8b6d2d65b102fd3745c0556.css integrity="sha256-549rbLOVvx6/91DK&#43;LSXzm8jxIX4ttLWWxAv03RcBVY="><link rel=stylesheet href=/css/base_fonts.css><link rel=stylesheet href=/css/jquery-ui.min.css><link rel=stylesheet href=/css/callouts.css><link rel=stylesheet href=/css/custom-jekyll/tags.css><meta name=description content="Kubernetes (K8s) is an open-source system for automating deployment, scaling, and management of containerized applications. It groups containers that make up an application into logical units for easy management and discovery. Kubernetes builds upon 15 years of experience of running production workloads at Google, combined with best-of-breed ideas and practices from the community.
   Planet Scale Designed on the same principles that allows Google to run billions of containers a week, Kubernetes can scale without increasing your ops team."><meta property="og:description" content="Kubernetes (K8s) is an open-source system for automating deployment, scaling, and management of containerized applications. It groups containers that make up an application into logical units for easy management and discovery. Kubernetes builds upon 15 years of experience of running production workloads at Google, combined with best-of-breed ideas and practices from the community.
   Planet Scale Designed on the same principles that allows Google to run billions of containers a week, Kubernetes can scale without increasing your ops team."><meta name=twitter:description content="Kubernetes (K8s) is an open-source system for automating deployment, scaling, and management of containerized applications. It groups containers that make up an application into logical units for easy management and discovery. Kubernetes builds upon 15 years of experience of running production workloads at Google, combined with best-of-breed ideas and practices from the community.
   Planet Scale Designed on the same principles that allows Google to run billions of containers a week, Kubernetes can scale without increasing your ops team."><meta property="og:url" content="https://kubernetes.io/">

```
---
---

# RBAC

```
vagrant@k8s-master:~$ kubectl create serviceaccount orbite
serviceaccount/orbite created

vagrant@k8s-master:~$ kubectl get serviceaccount
NAME      SECRETS   AGE
default   1         34d
orbite    1         13s

vagrant@k8s-master:~$ kubectl describe serviceaccount orbite
Name:                orbite
Namespace:           default
Labels:              <none>
Annotations:         <none>
Image pull secrets:  <none>
Mountable secrets:   orbite-token-9mnlj
Tokens:              orbite-token-9mnlj
Events:              <none>

vagrant@k8s-master:~$ kubectl get clusterrole
NAME                                                                   AGE
admin                                                                  34d
calico-kube-controllers                                                34d
calico-node                                                            34d
cluster-admin                                                          34d
edit                                                                   34d
system:aggregate-to-admin                                              34d
system:aggregate-to-edit                                               34d
system:aggregate-to-view                                               34d
system:auth-delegator                                                  34d
system:basic-user                                                      34d
... ect ....

vagrant@k8s-master:~$ kubectl describe clusterrole cluster-admin 
Name:         cluster-admin
Labels:       kubernetes.io/bootstrapping=rbac-defaults
Annotations:  rbac.authorization.kubernetes.io/autoupdate: true
PolicyRule:
  Resources  Non-Resource URLs  Resource Names  Verbs
  ---------  -----------------  --------------  -----
  *.*        []                 []              [*]
             [*]                []              [*]

vagrant@k8s-master:~$ kubectl create clusterrolebinding toskeira --serviceaccount=default:orbite --clusterrole=cluster-admin
clusterrolebinding.rbac.authorization.k8s.io/toskeira created

vagrant@k8s-master:~$ kubectl get clusterrolebindings.rbac.authorization.k8s.io
NAME                                                   AGE
calico-kube-controllers                                34d
calico-node                                            34d
cluster-admin                                          34d
kubeadm:kubelet-bootstrap                              34d
kubeadm:node-autoapprove-bootstrap                     34d
kubeadm:node-autoapprove-certificate-rotation          34d
kubeadm:node-proxier                                   34d
..... etc...

system:public-info-viewer                              34d
system:volume-scheduler                                34d
toskeira                                               64s

vagrant@k8s-master:~$ kubectl describe clusterrolebindings.rbac.authorization.k8s.io toskeira
Name:         toskeira
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  ClusterRole
  Name:  cluster-admin
Subjects:
  Kind            Name    Namespace
  ----            ----    ---------
  ServiceAccount  orbite  default

vagrant@k8s-master:~$ vim admin-user.yaml

apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system


vagrant@k8s-master:~$ kubectl create -f admin-user.yaml
serviceaccount/admin-user created

vagrant@k8s-master:~$ kubectl get service
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)                        AGE
giropops     NodePort    10.102.140.106   <none>        80:32222/TCP,32111:32111/TCP   13d
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP                        34d

vagrant@k8s-master:~$ kubectl get service -n kube-system
NAME       TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)                  AGE
kube-dns   ClusterIP   10.96.0.10   <none>        53/UDP,53/TCP,9153/TCP   34d

vagrant@k8s-master:~$ kubectl get serviceaccounts -n kube-system
NAME                                 SECRETS   AGE
admin-user                           1         2m
attachdetach-controller              1         34d
bootstrap-signer                     1         34d
calico-kube-controllers              1         34d
calico-node                          1         34d
certificate-controller               1         34d
clusterrole-aggregation-controller   1         34d
coredns                              1         34d
cronjob-controller                   1         34d
daemon-set-controller                1         34d
default                              1         34d
deployment-controller                1         34d
disruption-controller                1         34d
endpoint-controller                  1         34d
expand-controller                    1         34d
generic-garbage-collector            1         34d
horizontal-pod-autoscaler            1         34d
job-controller                       1         34d
kube-proxy                           1         34d
namespace-controller                 1         34d
node-controller                      1         34d
persistent-volume-binder             1         34d
pod-garbage-collector                1         34d
pv-protection-controller             1         34d
pvc-protection-controller            1         34d
replicaset-controller                1         34d
replication-controller               1         34d
resourcequota-controller             1         34d
service-account-controller           1         34d
service-controller                   1         34d
statefulset-controller               1         34d
token-cleaner                        1         34d
ttl-controller                       1         34d

vagrant@k8s-master:~$ vim admin-cluster-role-binding.yaml

apiVersion: rbac.authorization.k8.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system

  vagrant@k8s-master:~$ kubectl create -f admin-cluster-role-binding.yaml
clusterrolebinding.rbac.authorization.k8s.io/admin-user created

vagrant@k8s-master:~$ kubectl describe clusterrolebindings.rbac.authorization.k8s.io admin-user
Name:         admin-user
Labels:       <none>
Annotations:  <none>
Role:
  Kind:  ClusterRole
  Name:  cluster-admin
Subjects:
  Kind            Name        Namespace
  ----            ----        ---------
  ServiceAccount  admin-user  kube-system

```
---
---

# Helm

```
vagrant@k8s-master:~$ wget https://storage.googleapis.com/kubernetes-helm/helm-v2.12.3-linux-amd64.tar.gz
--2020-03-10 20:14:01--  https://storage.googleapis.com/kubernetes-helm/helm-v2.12.3-linux-amd64.tar.gz
Resolving storage.googleapis.com (storage.googleapis.com)... 172.217.172.208, 2800:3f0:4001:81c::2010
Connecting to storage.googleapis.com (storage.googleapis.com)|172.217.172.208|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 22722816 (22M) [application/x-tar]
Saving to: 'helm-v2.12.3-linux-amd64.tar.gz'

helm-v2.12.3-linux-amd64.tar.gz             100%[========================================================================================>]  21.67M  4.90MB/s    in 4.4s    

2020-03-10 20:14:06 (4.90 MB/s) - 'helm-v2.12.3-linux-amd64.tar.gz' saved [22722816/22722816]

vagrant@k8s-master:~$ sudo tar -vxzf helm-v2.12.3-linux-amd64.tar.gz
linux-amd64/
linux-amd64/LICENSE
linux-amd64/helm
linux-amd64/tiller
linux-amd64/README.md

vagrant@k8s-master:~/linux-amd64$ mv helm /usr/local/bin/

vagrant@k8s-master:~/linux-amd64$ sudo mv tiller /usr/local/bin/

vagrant@k8s-master:/$ curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get | bash
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100  7150  100  7150    0     0  20519      0 --:--:-- --:--:-- --:--:-- 20545
Helm v2.16.3 is available. Changing from version v2.12.3.
Downloading https://get.helm.sh/helm-v2.16.3-linux-amd64.tar.gz
Preparing to install helm and tiller into /usr/local/bin
helm installed into /usr/local/bin/helm
tiller installed into /usr/local/bin/tiller
Run 'helm init' to configure helm.
vagrant@k8s-master:/$ helm init --history-max 200
$HELM_HOME has been configured at /home/vagrant/.helm.

Tiller (the Helm server-side component) has been installed into your Kubernetes Cluster.

Please note: by default, Tiller is deployed with an insecure 'allow unauthenticated users' policy.
To prevent this, run `helm init` with the --tiller-tls-verify flag.
For more information on securing your installation see: https://docs.helm.sh/using_helm/#securing-your-helm-installation

vagrant@k8s-master:/$ helm init
$HELM_HOME has been configured at /home/vagrant/.helm.
Warning: Tiller is already installed in the cluster.
(Use --client-only to suppress this message, or --upgrade to upgrade Tiller to the current version.)

vagrant@k8s-master:/$ helm version
Client: &version.Version{SemVer:"v2.16.3", GitCommit:"1ee0254c86d4ed6887327dabed7aa7da29d7eb0d", GitTreeState:"clean"}
Server: &version.Version{SemVer:"v2.16.3", GitCommit:"1ee0254c86d4ed6887327dabed7aa7da29d7eb0d", GitTreeState:"clean"}

vagrant@k8s-master:/$ helm init --canary-image --upgrade
$HELM_HOME has been configured at /home/vagrant/.helm.

Tiller (the Helm server-side component) has been updated to gcr.io/kubernetes-helm/tiller:canary .

vagrant@k8s-master:/$ sudo apt-get upgrade helm
Reading package lists... Done
Building dependency tree       
Reading state information... Done
E: Unable to locate package helm

```

```
vagrant@k8s-master:/$ kubectl create serviceaccount --namespace=kube-system tiller
serviceaccount/tiller created

vagrant@k8s-master:/$ kubectl create clusterrolebinding tiller-cluster-role --clusterrole cluster-admin --serviceaccount=kube-system:tiller
clusterrolebinding.rbac.authorization.k8s.io/tiller-cluster-role created

vagrant@k8s-master:/$ kubectl get clusterrolebindings.rbac.authorization.k8s.io
NAME                                                   AGE
admin-user                                             44m
calico-kube-controllers                                34d
calico-node                                            34d
cluster-admin                                          34d

..... etc...
tiller-cluster-role                                    74s
toskeira                                               104m

vagrant@k8s-master:/$ kubectl patch deployments -n kube-system tiller-deploy -p '{"spec":{"template":{"apec":{"serviceAccount":"tiller"}}}}'
deployment.apps/tiller-deploy patched (no change)

vagrant@k8s-master:/$ helm list

vagrant@k8s-master:/$ helm search grafana
NAME          	CHART VERSION	APP VERSION	DESCRIPTION                                                 
stable/grafana	5.0.5        	6.6.2      	The leading tool for querying and visualizing time series...

vagrant@k8s-master:/$ helm search nginx
NAME                       	CHART VERSION	APP VERSION	DESCRIPTION                                                 
stable/nginx-ingress       	1.33.5       	0.30.0     	An nginx Ingress controller that uses ConfigMap to store ...
stable/nginx-ldapauth-proxy	0.1.3        	1.13.5     	nginx proxy with ldapauth                                   
stable/nginx-lego          	0.3.1        	           	Chart for nginx-ingress-controller and kube-lego            
stable/gcloud-endpoints    	0.1.2        	1          	DEPRECATED Develop, deploy, protect and monitor your APIs...











