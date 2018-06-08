---

copyright:
  years: 2017, 2018
lastupdated: "2018-05-24"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform

**ATENÇÃO:** antes de usar uma oferta do {{site.data.keyword.blockchainfull}} Platform, leia as informações técnicas e de suporte na seção [Renúncia de responsabilidade](needtoknow.html).  
{:shortdesc}

O {{site.data.keyword.blockchainfull_notm}} Platform é a única plataforma pronta para negócios integrada que aborda o ciclo de vida completo (**desenvolver**, **governar** e **operar**) de uma rede de blockchain de múltiplas organizações. Ele foi projetado para acelerar, por meio da colaboração em cada fase, a criação de redes de blockchain globais "construídas para os negócios" com o desempenho e a segurança até mesmo para os casos de uso e os setores regulamentados mais exigentes.

|  Função     | Atividades       | [Funções](#participating-in-a-blockchain-network)  |
| ------------------------- |--------------------------|-----|
| [Desenvolver](develop.html) | Desenvolver redes de negócios, desenvolver aplicativos, desenvolver chaincode | Desenvolvedores de rede, desenvolvedores de aplicativos |
| [Controlar](get_start.html)| Convidar membros, gerar credenciais, propor modelos de governança, disseminar certificados e informações do terminal | Operadores de rede, membros de rede |
| [Operar](v10_dashboard.html)| Monitorar funcionamento e atividade, gerenciar novas implementações, incluir ou remover membros, gerenciar o ciclo de vida do chaincode, gerenciar canais, suporte | Operadores de rede, participantes de rede |

**Nota**: a tabela não se destina a representar uma progressão linear. Tarefas como desenvolvimento e gerenciamento de rede ocorrerão repetidamente à medida que os aplicativos e a associação evoluírem.

## Ofertas de Associação da {{site.data.keyword.IBM_notm}}

O IBM Blockchain Platform fornece planos de associação diferentes para ajudar todos os tipos de usuários a começarem a sua jornada de blockchain e moverem os seus aplicativos para a produção.

|       | [Starter Plan](starter_plan.html)      | [Enterprise Plan](enterprise_plan.html)  | Plano corporativo plus | Plano autogerenciado
| ------------------------- |--------------------------|-----|-----|------|
| **O que está incluído** | **Níveis de serviço básico, ambiente de desenvolvimento e teste** | **Níveis de serviço avançados, prontos para produção corporativa** | **Cálculo dedicado para desempenho e isolamento, pronto para a produção corporativa** | Pilha de software para instalar em sua própria infraestrutura, conecte-se a uma rede hospedada nesse serviço de nuvem |
| **Política de faturamento** | **Grátis para beta<!--trial of 30 days, followed by monthly charge-->** | **Assinatura mensal** | **Assinatura mensal** | Assinatura mensal |
| **Disponibilidade** | **Beta** | **Disponível agora** | **Após a compra** | Em breve |


<!--
- **The IBM Blockchain Starter Plan** is ideal for those who are learning or getting started developing their blockchain network.
- **Enterprise Plan** is a production environment offering high levels of security and support.
- **Enterprise Plus Plan** offers a dedicated production environment for additional performance and isolated compute and storage resources to protect critical data.
-->

**Cuidado:** não use o **Starter Plan** para a produção. Ele é um ambiente de desenvolvimento e teste.

As ofertas do {{site.data.keyword.blockchainfull_notm}} são construídas no código base do [Hyperledger Fabric](./v10_fabric) que alavanca uma arquitetura modular para atingir níveis corporativos de segurança, integridade de dados, escalabilidade e desempenho para atender às suas necessidades de negócios.
- As redes do **Starter Plan** são construídas no Hyperledger Fabric V1.1.
- O **Enterprise Plan** é atualizado para o Hyperledger Fabric V1.1. Todas as redes recém-criadas estão no nível do Fabric V1.1.

Inscreva-se agora para participação no [{{site.data.keyword.blockchainfull_notm}}
![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps)!


## **Desenvolver** a rede
Explorar e acelerar o desenvolvimento de blockchain alavancando as melhores práticas que são adquiridas em mais de 400 engajamentos de clientes para:
* Assegurar o alinhamento estrito nos negócios utilizando uma tecnologia que reduz significativamente o tempo de desenvolvimento do aplicativo (aplicativos que de outra forma levariam seis semanas para serem desenvolvidos podem ser criados em menos de dois dias).
* Construir rapidamente habilidades de blockchain em sua lista existente de programadores utilizando as ferramentas de desenvolvedor do {{site.data.keyword.blockchainfull_notm}}.
* Fornecer aos desenvolvedores a flexibilidade para aprender e desenvolver em seus ambientes preferenciais com um conjunto de ferramentas aberto e moderno.  

Como um proprietário de negócios, é possível desenvolver o seu caso de uso com a ajuda da profunda bancada de especialistas em segmento de mercado e blockchain da IBM que se reúnem no {{site.data.keyword.blockchainfull_notm}} Garage para aproveitar o poder total do {{site.data.keyword.blockchainfull_notm}} Platform.

Como um desenvolvedor, é possível alinhar os requisitos de negócios de maneira rápida e fácil e acelerar o desenvolvimento de aplicativos de blockchain no ambiente de rede do {{site.data.keyword.blockchainfull_notm}} Platform usando um playground interativo para desenvolver, iterar e testar redes de negócios. Essas ferramentas foram projetadas para transformar regras de negócios em código de rede de negócios em seu ambiente preferencial:
* **Explorar on-line**  
  Alavanque o [{{site.data.keyword.blockchainfull_notm}} Platform: Develop](./develop.html)<!--, which is powered by an open source development tool--> para aprender conceitos chaves de blockchain, criar definições de rede e alavancar modelos do segmento de mercado reutilizáveis e bibliotecas de contrato inteligentes.  
  Após você ter desenvolvido a sua rede de negócios, será possível implementá-la em uma rede em tempo real em execução no IBM Blockchain Platform. Para obter mais informações, veja [Implementando redes de negócios com o Starter Plan](./develop_starter.html) e [Implementando redes de negócios com o Enterprise Plan](./develop_enterprise.html).
* **Instale localmente**  
  Alavanque as imagens certificadas da IBM do Hyperledger Fabric e Composer, que são estrutura de software livre e ferramenta para construir uma rede de negócios, para desenvolver e testar diretamente em seu laptop. Para obter mais informações, veja [Construindo a sua primeira rede](http://hyperledger-fabric.readthedocs.io/en/latest/build_network.html).
* **Colabore em um ambiente de nuvem**  
  Use redes em tempo real prontas para uso com opções do Starter Plan e Enterprise Plan para desenvolver e compartilhar o seu código com outros. Para obter mais informações, veja [Sobre o Starter Plan](starter_plan.html) e [Sobre o Enterprise Plan](enterprise_plan.html).


## **Controle** a rede
Há duas opções para criar o ambiente de backend de sua rede. Primeiro, é possível usar as imagens do Hyperledger Docker publicadas, fornecendo a opção de implementar a biblioteca do Composer para construir seus aplicativos e interagir com sua rede. Ou é possível gravar o chaincode nativamente e desenvolver o código do lado do servidor para conduzir as transações. A execução local é a oportunidade perfeita para mexer com as configurações de rede, explorar casos de uso potenciais e começar a construir a provas de conceito. Quando o seu PoC começar a tomar forma, será possível ampliar a implementação hospedando a sua rede na nuvem.

Com uma implementação de nuvem, é fornecida a você uma coleção de receitas e scripts fáceis de usar para facilitar a implementação de uma rede do Hyperledger Fabric que é executado no Kubernetes. Use esta fase para examinar o comportamento e a estabilidade de sua PoC em um ambiente hospedado. O [{{site.data.keyword.blockchainfull_notm}} Container Service ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://ibm-blockchain.github.io/) pode ser considerado um mecanismo de teste para a funcionalidade e a resiliência de seu aplicativo e como um precursor natural para o Enterprise Plan.

Assim que você tiver uma rede, o {{site.data.keyword.blockchainfull_notm}} foi projetado para criar uma experiência de gerenciamento de rede que oferece algum controle aos membros, embora nenhum membro único esteja no controle total. O {{site.data.keyword.blockchainfull_notm}} Platform tem o primeiro conjunto de ferramentas integradas para permitir que as equipes impinjam o gerenciamento de mudanças da rede por meio de políticas customizáveis.

A lista a seguir mostra os recursos-chave desse modelo de governança:

* Ferramentas de gerenciamento democráticas para permitir o gerenciamento coletivo.
* Editor de Política para definir políticas flexíveis e democráticas para controlar as mudanças na rede.
* Ferramentas e políticas pré-construídas para ativar a migração, a customização e a ativação mais rápidas.
* Uma ferramenta de fluxo de trabalho multipartes com notificações integradas, um painel de atividades do membro e a coleta de assinatura segura.


## **Opere** a rede
Implemente e opere redes descentralizadas com um serviço seguro pronto para produção. Comece pequeno e escale sua rede elasticamente à medida que seus volumes de associação e de transação aumentam, aproveitando os recursos a seguir:

* Um ambiente de segurança ultra alta com muitos recursos de segurança de hardware, de firmware e de software.
* Arquitetura protegida para escalabilidade, resiliência e disponibilidade.
* Otimizado para desempenho e execução na computação do Linux mais rápida do mundo.


A operação de sua rede no {{site.data.keyword.blockchainfull_notm}} Platform inclui ferramentas e recursos para simplificar tarefas administrativas:

* Painéis para monitorar e gerenciar os recursos na rede.
* Upgrades ininterruptos da pilha de código integral.
* Suporte técnico 24/7 integrado no portal.
* Pilha de segurança protegida sem acesso privilegiado, resistência a malware e violação, 100% de criptografia e muito mais recursos para redes com dados sensíveis em setores regulamentados.

## Serviços de rede básicos

Para tornar um blockchain operacional, os membros formam uma base de confiança, executando um ou mais serviços de rede básicos:

- **Serviço de solicitação** – Solicitando e sincronizando transações  
  Essencialmente, o serviço de solicitação é a definição da rede.  Ele contém informações de identidade para cada membro, informações sobre canais e um conjunto de políticas que ditam quais membros têm permissão para executar determinadas tarefas (por exemplo, convidar outros membros, criar canais etc.). Cada operação de transação e configuração fluirá por meio do serviço de solicitação, portanto, ele é uma parte mais do que crítica no esquema geral das coisas.  Dada a importância fundamental do serviço de solicitação, é fácil ver os perigos de uma orquestração autoritária na qual, talvez, apenas um membro dite as regras.  Para combater isso, o serviço de solicitação é gerenciado comunitariamente pelos membros da rede e as implementações de governança são aplicadas em conjunto.  Por outras palavras, as decisões são tomadas coletivamente, NÃO unilateralmente. Todos os membros têm uma participação na rede e, por extensão, têm um voto em quaisquer operações que configurem e customizem sua posição na rede.  Essas noções de "democracia" e decisões tomadas em conjunto são os blocos de construção inerentes para uma rede confiável e descentralizada.  A IBM serve como o "operador" do serviço de solicitação para quaisquer redes que são implementadas no IBM Blockchain Platform.

- **Autoridade de certificação** – Emitindo certificados para os participantes  
  Simplificando, a Autoridade de Certificação (CA) fornece a associação.  Todas as entidades na rede (peers, solicitantes, clientes etc.) deve ter uma identidade para se comunicar, autenticar e, finalmente, transacionar. Essas “identidades“ existem na forma de certificados x509 (ou seja, certificados de inscrição), que são necessários para qualquer participação direta na rede de blockchain.  Também há formas de participação indireta, mas vamos falar sobre isso mais tarde.  A CA pode ser mais bem entendida como o carimbo que fornece atestado e credibilidade às identidades.  Cada membro possui sua própria autoridade de certificação e, por meio dela, ele pode assinar certificados não apenas para os seus recursos de propriedade total (peers), mas também para clientes e aplicativos de terceiros.  Você pode comparar a CA de um membro a uma caneta especial ou a um carimbo do tabelião.  Um certificado de que essas assinaturas de autoridade de certificação são um pré-requisito para acessar a rede.

- **Peer** – Validando/endossando transações  
  O peer existe para executar duas funções principais: executar/validar transações e manter livros-razão.  O peer executa contratos inteligentes e é o portador da história da transação e do estado atual de ativos nos canais da rede.  No final do dia, basta acessar o peer (direta ou indiretamente) e executar leituras e gravações com relação ao livro-razão. Quando um membro fornece um acesso de usuário à rede, ele realmente está fornecendo acesso à funcionalidade do peer.

Quando um membro se associa a uma rede por meio do {{site.data.keyword.blockchainfull_notm}} Platform, a emissão padrão é um Peer e uma CA.  Para uma rede de produção, os membros desejarão executar várias instâncias desses serviços para assegurar a disponibilidade. Por padrão, a IBM executa o Serviço de solicitação para redes que o {{site.data.keyword.blockchainfull_notm}} Platform cria.  

## Participando de uma rede de blockchain

O termo **participante** é a classificação mais ampla para qualquer organização, indivíduo, aplicativo ou dispositivo que interage com a rede de blockchain.  Sob o guarda-chuva "participante", há dois agrupamentos distintos, ou seja, **membros** e **usuários**.   

Em termos práticos, um membro possui um certificado digital válido, que permite que ele emita e/ou valide transações em uma rede de blockchain.  Um usuário não tem um certificado, mas ele ainda pode interagir com a rede de blockchain por meio de um dos membros de rede existentes.   Você pode considerar um certificado do membro como seu “cartão de sócio“ para um clube de ginástica.  Os usuários não podem ter tal cartão de associação, mas eles podem entrar no clube de ginástica como os "guests" de um membro existente.  Vamos ver essas funções um pouco mais de perto.

### Membros

O {{site.data.keyword.blockchainfull_notm}} Platform é suportado pelo Hyperledger Fabric, que é uma tecnologia de "blockchain com permissão".  Portanto, todos os membros são inscritos na rede com um certificado que lhes concede permissões para usar a rede como um serviço **provedor** (ou seja, emitir certificados, validar/pedir transações) ou **consumidor** (ou seja, emitir transações).   

- **Provedores - Nós confiamos nos membros** - Uma rede de blockchain é desenvolvida pelos seus membros.   Para tornar uma rede de blockchain operacional, precisa haver um conjunto mínimo de membros que *forneça* serviços de blockchain fundamentais, incluindo validação de transação, solicitação de transação e serviços de gerenciamento de certificado. Executando esses serviços, esses membros se tornam mantenedores da integridade do livro-razão compartilhado no epicentro da rede de blockchain. Então, de quantos membros você precisa para tornar um blockchain operacional? A resposta é: depende do requisito de confiança da rede. Algumas redes toleram um modelo de confiança mais centralizado que requer menos membros para servir como provedores.  Outras redes requerem um conjunto de membros mais diversificado (ou seja, entidades separadas legalmente) e mantêm um modelo de confiança mais descentralizado.  Um exemplo de um modelo de confiança mais centralizado seria uma rede de visibilidade da cadeia de suprimento cujos membros de fornecimento são um varejista global, uma companhia de navegação global e a IBM.  Nesse caso, esses três membros agiriam como a “base de confiança“ para a rede, fornecendo os serviços básicos da rede de blockchain. Esses membros podem emitir certificados para importadores, exportadores, agentes customizados e varejistas, de modo que eles possam participar (emitir transações) da rede. Esta rede pode descentralizar a confiança, permitindo que mais membros participem do fornecimento de serviços básicos, e, portanto, garantindo que todos os membros tenham controle, mas nenhum membro único tenha controle exclusivo. Uma rede típica tem cerca de 10 membros que fornecem serviços de blockchain de base.

- **Consumidores - A confiança é estabelecida, agora chame à distância** - Quando uma base de confiança é estabelecida, a rede está pronta para crescer.  É comum que a maioria dos membros em sua rede esteja simplesmente usando a rede para chamar transações com relação ao livro-razão distribuído.  Esses membros são simplesmente *consumidores* e não estão participando do fornecimento de serviços básicos à rede.  Uma rede típica tem em torno de 10 a 100 membros com a permissão para emitir transações dentro de uma rede de blockchain especificada.


#### Membros personas

Às vezes é útil pensar nos membros como uma persona que descreve a função do membro na rede de negócios.  Aqui estão alguns que são frequentemente usados.

- **Iniciador** - Um membro que outros membros selecionam para autoinicializar a rede de Blockchain.    O IBM Blockchain Platform requer que um único membro se conecte a ele e execute tarefas para iniciar a rede.  Elas incluem nomenclatura da rede, convite do conjunto de membros inicial e configuração do conjunto padrão de políticas de operação de rede.  Esta é uma função temporária.  Após a rede ser reinicializada, o inicializador não reterá privilégios especiais e simplesmente continuará a função de membro.  

- **Mantenedor** - Um membro que está executando um ou mais peers e autoridades de certificação de rede.   Esses membros estão mantendo a integridade do livro-razão distribuído pela participação no processo de consenso, que é como as transações são validadas em uma rede de blockchain.   O mantenedor, por meio da propriedade de uma autoridade de certificação, também pode emitir certificados aos participantes e conceder-lhes acesso à rede.

- **Operador** - Um membro que está executando serviços em nome de outros membros de rede, incluindo o serviço de solicitação de transação, as autoridades de certificação, os gateways de transação e outros serviços de rede básicos.  Por padrão, a IBM é o operador de redes que são implementadas no IBM Blockchain Platform.

- **Auditor** - Um membro que tem permissões concedidas pela rede para executar funções de auditoria na rede.  Exemplos de funções de auditoria incluem faturamento, rastreamento de conformidade ou analítica.  A função de auditor geralmente se traduz no membro que tem acesso a uma visualização mais ampla das transações no livro-razão e/ou uma inscrição mais ampla em canais de transação.

### Usuários

Embora possa haver centenas de membros em uma rede de blockchain, pode haver milhares de usuários.   Um usuário é um participante em uma rede de blockchain que tem acesso indireto ao livro-razão por meio de um “relacionamento de confiança“ com um membro existente.  Por exemplo, é comum para alguns aplicativos móveis implementar sua própria autenticação do usuário e o esquema de autorização (OAuth, OpenID) e mapear essas credenciais para um ou mais membros credenciados em uma rede de blockchain.    Um serviço de proxy ou de gateway normalmente é criado para executar essa função de mapeamento para mapear o mundo externo para o mundo do blockchain.

## O {{site.data.keyword.blockchainfull_notm}} Platform

O {{site.data.keyword.blockchainfull_notm}} Platform fornece uma rede de blockchain altamente segura e com permissões na qual os membros autenticados podem definir ativos facilmente e criar as soluções de negócios para modificar e trocá-los.  Inicializar uma rede de blockchain no nível de produção não é mais um processo tedioso e complicado. Com o {{site.data.keyword.blockchainfull_notm}} Platform, é possível aproveitar uma estrutura de orquestração que permite **organizar o seu consórcio em uma rede de blockchain ativa em tempo recorde**. O {{site.data.keyword.blockchainfull_notm}} Platform fornece consórcio amigável de ferramentas que é projetado para facilitar a reunião de múltiplas instituições para unir e criar uma rede controlada democraticamente. A criação, governança e tarefas de operação da rede se tornam intuitivas e transparentes por meio de um monitor de painel integrado e de utilitários provisionados. Em vez de passar pelo processo complicado de criar uma rede e implementar a governança, os membros do consórcio podem **focar na implementação de contratos inteligentes e transferência de ativos e informações**.      
{:shortdesc}

A **Alta disponibilidade** para os recursos integrais da rede (peers, serviço de solicitação, Autoridade de Certificação, chaincode) elimina os efeitos incapacitantes que podem surgir de pontos únicos de falha. Um monitor de painel integrado permite o fácil gerenciamento desses recursos e fornece um mecanismo poderoso para visualizar os ativos e contratos inteligentes.

A **modularidade** da arquitetura do Hyperledger Fabric e a separação distinta de funções de rede (endossante, responsável, solicitante) fornecem uma infraestrutura que permite a escalabilidade e a adaptabilidade para uma ampla variedade de casos de uso.

As verificações e os saldos que ocorrem em todo o ciclo de vida de uma transação asseguram resultados consistentes e completamente avaliados e os livros-razão permanecem constantemente sincronizados por meio de uma implementação do protocolo de fofocas bem conhecido. A identidade e o controle de acesso são impingidos facilmente por meio das operações de **assinatura/verificação** que ocorrem permanentemente em toda a rede.  

É fornecido um **conjunto de ferramentas de governança** para permitir que os membros administrem e gerenciem as regras de negócios críticas para suas redes. Por exemplo, talvez você queira implementar uma política que defina quantos membros de uma rede devem concordar para que um novo membro se associe. Ou, talvez haja um ativo que requeira aprovação de cada participante para que uma modificação ocorra. As regras de governança são fundamentais para qualquer tipo de rede de negócios e geralmente são extremamente elaboradas. O conjunto de ferramentas de governança (por exemplo, editores de política) simplifica bastante esse processo.

O serviço é executado em um ambiente **altamente seguro e isolado** sem acesso externo (incluindo acesso raiz) para recursos de rede. Os dados são criptografados em andamento e em repouso e os módulos de suporte de segurança de hardware permitem que chaves digitais sejam protegidas em conformidade com os regulamentos do segmento de mercado. A virtualização de hardware é usada para executar cada nó em um ambiente isolado, protegendo, assim, outros nós na rede contra peers com chaincode potencialmente inadequado ou malicioso. O hashing, as operações de assinatura/verificação e as comunicações de nó para nó são acelerados graças às implementações avançadas de criptografia.

Antes de continuar, vamos dar uma olhada em uma configuração simples dentro do {{site.data.keyword.blockchainfull_notm}} Platform.  A **Figura 1** mostra um exemplo de uma rede de blockchain implementada que consiste em quatro membros (cada um possui dois peers), uma Autoridade de certificação que é responsável por distribuir o material de identidade criptográfico e um serviço de solicitação que define políticas e participantes de rede. O canal azul contém todos os quatro membros de rede, enquanto que o canal amarelo é restrito a somente três membros: os Bancos B, C e D. Também é possível ver que o Banco A exerceu a função de inicializador de rede e que o Banco D existe somente como um membro no contexto do canal amarelo. Finalmente, um usuário ou aplicativo em posse de um certificado x509 devidamente assinado é capaz de enviar chamadas para peers na rede. Conforme mencionado anteriormente, é bem possível que um usuário nem mesmo saiba sobre a existência da rede de blockchain.

![Rede de Blockchain](images/blockchain_network_2-01.png "Exemplo de rede de blockchain")

*Figura 1. Uma rede de blockchain de exemplo consiste em quatro membros que alavancam canais para isolar dados*

## Suporte {{site.data.keyword.IBM_notm}}

A {{site.data.keyword.IBM_notm}} oferece suporte para as soluções {{site.data.keyword.blockchain}} implementadas pela {{site.data.keyword.IBM_notm}}. Para obter mais informações sobre o Suporte {{site.data.keyword.blockchainfull_notm}}, consulte [Obtendo suporte](ibmblockchain_support.html).

Para obter mais detalhes sobre todos os recursos e a funcionalidade do Hyperledger Fabric,
veja [Documentação do Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric.readthedocs.io/en/latest/).
