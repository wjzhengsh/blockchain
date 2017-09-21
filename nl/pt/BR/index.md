---

copyright:
  years: 2017
lastupdated: "2017-08-24"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# {{site.data.keyword.blockchainfull_notm}} Platform

**ATENÇÃO:** antes de usar uma oferta {{site.data.keyword.blockchainfull}}, leia as informações técnicas e de suporte na seção [Renúncia de Responsabilidade](needtoknow.html).  
{:shortdesc}

O {{site.data.keyword.blockchainfull_notm}} Platform é a única plataforma integrada pronta para negócios que aborda o ciclo de vida completo (**desenvolver**, **administrar** e **operar**) de uma rede de blockchain de diversas organizações. Ele foi projetado para acelerar, por meio da colaboração em cada fase, a criação de redes de blockchain globais "construídas para os negócios" com o desempenho e a segurança até mesmo para os casos de uso e os setores regulamentados mais exigentes. As ofertas de {{site.data.keyword.blockchainfull_notm}} são construídas no código base do Hyperledger Fabric V1.0 alavancando uma arquitetura modular para atingir níveis corporativos de segurança, integridade de dados, escalabilidade e desempenho para atender às suas necessidades de negócios.  

Vamos dar uma olhada nas atividades e funções primárias em uma rede de {{site.data.keyword.blockchainfull_notm}} e ver como elas se ajustam ao ciclo de vida de uma rede.

**Nota**: a tabela a seguir não é destinada a representar uma progressão linear. Tarefas como desenvolvimento e gerenciamento de rede ocorrerão repetidamente à medida que os aplicativos e a associação evoluírem.

|  Função     | Atividades       | Funções  |
| ------------------------- |--------------------------|-----|
| [Desenvolver ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://developer.ibm.com/blockchain/sandbox/) | Desenvolver Aplicativos, Desenvolver Chaincode | Desenvolvedores de Aplicativos |
| [Controlar](get_start.html)| Convidar Membros, Gerar Credenciais, Propor Modelos de Governança, Associação de Novos Membros, Disseminar informações de certificados e de terminal | Operadores de Rede, Membros da Rede |
| [Operar](v10_dashboard.html)| Monitorar o funcionamento e a atividade, Gerenciar novas implementações, Gerenciar membros (inclusão/remoção), Gerenciar ciclo de vida de chaincode, Gerenciar canais, Suporte | Operadores de Rede, Participantes da Rede |
  
Agora vamos dar uma olhada mais de perto nessas funções e atividades...

## **Desenvolver** a rede
Explore e acelere o desenvolvimento de blockchain alavancando as melhores práticas adquiridas em mais de 400 engajamentos de clientes para:
* Assegurar o alinhamento estrito nos negócios utilizando uma tecnologia que reduz significativamente o tempo de desenvolvimento do aplicativo (aplicativos que de outra forma levariam seis semanas para serem desenvolvidos podem ser criados em menos de dois dias).
* Construir rapidamente habilidades de blockchain em sua lista existente de programadores utilizando as ferramentas de desenvolvedor do {{site.data.keyword.blockchainfull_notm}}.
* Fornecer aos desenvolvedores a flexibilidade para aprender e desenvolver em seus ambientes preferenciais com um conjunto de ferramentas aberto e moderno.  
  
Como um proprietário de negócios, é possível desenvolver seu caso de uso com a ajuda da bancada de especialistas da indústria e do blockchain da IBM que se reúnem no {{site.data.keyword.blockchainfull_notm}} Garage para aproveitar o poder total do IBM Blockchain Platform. 

Como um desenvolvedor, é possível alinhar os requisitos de negócios de maneira rápida e fácil e acelerar o desenvolvimento de aplicativos de blockchain de graça com um ambiente de simulação em nuvem e um playground interativo que transformam qualquer programador em um desenvolvedor de blockchain. Essas ferramentas foram projetadas para transformar regras de negócios em código em seu ambiente preferencial:
* **Explorar on-line**  
  Utilize o [Hyperledger Composer ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://hyperledger.github.io/composer/introduction/introduction.html), que é uma ferramenta de desenvolvimento de software livre para aprender conceitos de blockchain chave, para criar definições de rede e para alavancar modelos da indústria reutilizáveis e bibliotecas de contratos inteligentes. 
* **Instale localmente**  
  Alavanque as imagens certificadas da IBM do Hyperledger Fabric e Composer, que é uma estrutura de software livre para construir uma rede de negócios, para desenvolver e testar diretamente em seu laptop.
* **Colabore em um ambiente de nuvem**  
  Opções grátis e com taxa para desenvolver e compartilhar seu código com terceiros.
  
Assim que você tiver desenvolvido sua rede de negócios, poderá implementá-la em uma rede em tempo real em execução no IBM Blockchain Platform com esta [receita ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://ibm-blockchain.github.io/platform-deployment/).

## **Controle** a rede
Há duas opções para criar o ambiente de backend de sua rede. Primeiro, é possível usar as imagens do Hyperledger Docker publicadas, fornecendo a opção de implementar a biblioteca do Composer para construir seus aplicativos e interagir com sua rede. Ou, é possível gravar o chaincode nativamente e desenvolver o código do lado do servidor para conduzir as transações. A execução local é a oportunidade perfeita para mexer com as configurações de rede, explorar casos de uso potenciais e começar a construir a provas de conceito. À medida que sua PoC começar a tomar forma, será possível ampliar a implementação, hospedando sua rede na nuvem.

Com uma implementação de nuvem, você recebe uma coleção de receitas e scripts fáceis de usar para facilitar a implementação de uma rede do Hyperledger Fabric em execução no Kubernetes. Use esta fase para examinar o comportamento e a estabilidade de sua PoC em um ambiente hospedado. O [{{site.data.keyword.blockchainfull_notm}} Container Service ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://ibm-blockchain.github.io/) pode ser considerado um mecanismo de teste para a funcionalidade e a resiliência de seu aplicativo e como um precursor natural para o Plano Corporativo.

Assim que você tiver uma rede, o {{site.data.keyword.blockchainfull_notm}} foi projetado para criar uma experiência de gerenciamento de rede que oferece algum controle aos membros, embora nenhum membro único esteja no controle total. O {{site.data.keyword.blockchainfull_notm}} Platform tem o primeiro conjunto de ferramentas integradas para permitir que as equipes impinjam o gerenciamento de mudanças da rede por meio de políticas customizáveis. 

A lista a seguir mostra os recursos-chave desse modelo de governança:

* Ferramentas de gerenciamento democráticas que permitem o gerenciamento coletivo.
* Editor de Política para definir políticas flexíveis e democráticas para controlar as mudanças na rede.
* Ferramentas e políticas pré-construídas permitem a migração, a customização e a ativação mais rápidas.
* Uma ferramenta de fluxo de trabalho multipartes com notificações integradas, um painel de atividades do membro e a coleta de assinatura segura.

## **Opere** a rede
Implemente e opere redes descentralizadas com um serviço seguro pronto para produção. Comece pequeno e escale sua rede elasticamente à medida que seus volumes de associação e de transação aumentam, aproveitando os recursos a seguir:

* Um ambiente de segurança ultra alta com muitos recursos de segurança de hardware, de firmware e de software.
* Arquitetura protegida para escalabilidade, resiliência e disponibilidade.
* Otimizado para desempenho e execução na computação do Linux mais rápida do mundo.
  
A operação de sua rede no {{site.data.keyword.blockchainfull_notm}} Platform inclui ferramentas e recursos para simplificar tarefas administrativas:

* Painéis para monitorar e gerenciar os recursos na rede. 
* Gerenciamento de ciclo de vida para upgrades contínuos da pilha de código completa.
* Suporte técnico 24/7 integrado no portal.
* Pilha de segurança protegida sem acesso privilegiado, resistência a malware e violação, 100% de criptografia e muito mais recursos para redes com dados sensíveis em setores regulamentados.

## Participando de uma rede de blockchain 

Usamos o termo **participante** como a classificação mais ampla para qualquer organização, indivíduo, aplicativo ou dispositivo que interage com a rede de blockchain. Sob o grupo “participante“ há dois agrupamentos distintos – **membros** e **usuários**.   
 
Em termos práticos, um membro possui um certificado digital válido, que permite que ele emita e/ou valide transações em uma rede de blockchain. Um usuário não tem um certificado, mas ele ainda pode interagir com a rede de blockchain por meio de um dos membros de rede existentes. Você pode considerar um certificado do membro como seu “cartão de sócio“ para um clube de ginástica. E, embora um usuário não tenha um cartão de sócio, ele pode entrar no clube de ginástica como o “convidado” de um membro existente. Vamos ver essas funções um pouco mais de perto.

### Membros 

O {{site.data.keyword.blockchainfull_notm}} Platform é suportado pelo Hyperledger Fabric, que é uma tecnologia de “blockchain com permissão“. Portanto, todos os membros são inscritos na rede com um certificado que lhes concede permissões para usar a rede como um **provedor** de serviços (ou seja, emitir certificados, validar/ordenar transações) ou **consumidor** (ou seja, emitir transações).   

- **Provedores - Nós confiamos nos membros** - Uma rede de blockchain é desenvolvida pelos seus membros. Para tornar uma rede de blockchain operacional, precisa haver um conjunto mínimo de membros *fornecendo* serviços de blockchain básicos, incluindo validação de transação, solicitação de transação e serviços de gerenciamento de certificado. Executando esses serviços, esses membros se tornam mantenedores da integridade do livro razão compartilhado no epicentro da rede de blockchain. Então, de quantos membros você precisa para tornar um blockchain operacional? A resposta é: depende do requisito de confiança da rede. Algumas redes toleram um modelo de confiança mais centralizado que requer menos membros servindo como provedores. Outras redes requerem um conjunto de membros mais diversificado (isto é, entidades separadas legalmente) e mantêm um modelo de confiança mais descentralizado. Um exemplo de um modelo de confiança mais centralizado seria uma rede de visibilidade da cadeia de suprimento cujos membros de fornecimento são um varejista global, uma companhia de navegação global e a IBM. Nesse caso, esses três membros agiriam como a “base de confiança“ para a rede, fornecendo os serviços básicos da rede de blockchain. Esses membros podem emitir certificados para importadores, exportadores, agentes aduaneiros e varejistas, de modo que eles possam participar (emitir transações) na rede. Esta rede pode descentralizar a confiança, permitindo que mais membros participem do fornecimento de serviços básicos, garantindo, assim, que todos os membros tenham controle, mas nenhum membro único tenha controle exclusivo. Uma rede típica terá na ordem de 10 dos membros fornecendo serviços de blockchain de base.

- **Consumidores - Confiança estabelecida, agora é só chamar** - Com uma base de confiança estabelecida, a rede está pronta para crescer. É muito comum que a maioria dos membros em sua rede esteja simplesmente usando a rede para chamar transações no livro razão distribuído. Esses membros são simplesmente *consumidores* e não estão participando do fornecimento de serviços básicos à rede. Uma rede típica terá a ordem de 10 a 100 membros fornecendo a permissão para emitir transações dentro de uma rede de blockchain especificada.


#### Membros personas
 
Às vezes é útil pensar nos membros como uma persona que descreve a função do membro na rede de negócios. Aqui estão alguns que são frequentemente usados.
 
- **Inicializador** – Um membro que foi selecionado por outros membros para autoinicializar a rede de Blockchain. O IBM Blockchain Platform requer que um único membro se conecte a ele e execute tarefas para iniciar a rede. Elas incluem nomenclatura da rede, convite do conjunto de membros inicial e configuração do conjunto padrão de políticas de operação de rede. Esta é uma função temporária. Assim que a rede é reinicializada, o inicializador não retém privilégios especiais e simplesmente retoma a função de membro.  

- **Mantenedor** – Um membro que está executando um ou mais Peers e CAs de rede. Esses membros estão mantendo a integridade do livro razão distribuído pela participação no processo de consenso, que é como as transações são validadas em uma rede de blockchain. O mantenedor, por meio da propriedade de uma CA, também tem a capacidade de emitir certificados aos participantes e conceder-lhes acesso à rede. 
 
- **Operador** – Um membro que está executando serviços em nome de outros membros de rede, incluindo o serviço de solicitação de transação, as autoridades de certificação, os gateways de transação e outros serviços de rede básicos. Por padrão, a IBM é o operador de redes implementadas no IBM Blockchain Plataform.
 
- **Auditor** – Um membro que recebeu permissões pela rede para executar funções de auditoria na rede. Exemplos de funções de auditoria incluem faturamento, rastreamento de conformidade ou analítica. A função de auditor geralmente se traduz no membro tendo acesso a uma visualização mais ampla das transações no livro razão e/ou uma inscrição mais ampla em canais de transação.

### Usuários

Embora possa haver centenas de membros em uma rede de blockchain, pode haver milhares de usuários. Um usuário é um participante em uma rede de blockchain que tem acesso indireto ao livro razão por meio de um “relacionamento de confiança“ com um membro existente. Por exemplo, é comum para alguns aplicativos móveis implementar sua própria autenticação do usuário e o esquema de autorização (OAuth, OpenID) e mapear essas credenciais para um ou mais membros credenciados em uma rede de blockchain. Um serviço de proxy ou de gateway normalmente é criado para executar essa função de mapeamento, mapeando, assim, o mundo exterior para o mundo do blockchain.

## Serviços de rede básicos 

Para tornar um blockchain operacional, os membros formam uma base de confiança, executando um ou mais serviços de rede básicos: 

- **Serviço de solicitação** – Solicitando e sincronizando transações  
  Essencialmente, o serviço de solicitação é a definição da rede. Ele contém informações de identidade para cada membro, informações sobre canais e um conjunto de políticas que ditam quais membros têm permissão para executar determinadas tarefas (por exemplo, convidar outros membros, criar canais, etc.). Cada operação de transação e configuração fluirá por meio do serviço de solicitação, portanto, ele é uma parte mais do que crítica no esquema geral das coisas. Dada a importância fundamental do serviço de solicitação, é fácil ver os perigos de uma orquestração autoritária na qual, talvez, apenas um membro dite as regras. Para combater isso, o serviço de solicitação é gerenciado comunitariamente pelos membros da rede e as implementações de governança são aplicadas em conjunto. Por outras palavras, as decisões são tomadas coletivamente, NÃO unilateralmente. Todos os membros têm uma participação na rede e, por extensão, têm um voto em quaisquer operações que configurem e customizem sua posição na rede. Essas noções de “democracia“ e decisões tomadas em conjunto são os blocos de construção inerentes para uma rede confiável e descentralizada. A IBM serve como o "operador" do serviço de solicitação para quaisquer redes implementadas no IBM Blockchain Plataform.
 
- **Autoridade de certificação** – Emitindo certificados para os participantes  
  Simplificando, a Autoridade de Certificação (CA) fornece a associação. Todas as entidades na rede (peers, solicitantes, clientes, etc.) devem ter uma identidade para se comunicar, autenticar e, por fim, transacionar. Essas “identidades“ existem na forma de certificados x509 (ou seja, certificados de inscrição), que são necessários para qualquer participação direta na rede de blockchain. Também há formas de participação indireta, mas vamos falar sobre isso mais tarde. A CA pode ser mais bem entendida como o carimbo que fornece atestado e credibilidade às identidades. Cada membro possui sua própria CA e, por meio dessa CA, ele pode assinar certificados não apenas para seus componentes de propriedade (peers), mas também para clientes e aplicativos de terceiros. Você pode comparar a CA de um membro a uma caneta especial ou a um carimbo do tabelião. Um certificado assinado por essa CA é pré-requisito para acessar a rede.
 
- **Peer** – Validando/endossando transações  
  O peer existe para executar duas funções principais: executar/validar transações e manter livros razão. O peer executa contratos inteligentes e é o portador da história da transação e do estado atual de ativos nos canais da rede. No final do dia, é só acessar o peer (direta ou indiretamente) e executar leituras e gravações no livro razão. Quando um membro fornece um acesso de usuário final à rede, ele está de fato fornecendo acesso à funcionalidade do peer.

Quando um membro se associa a uma rede por meio do {{site.data.keyword.blockchainfull_notm}} Platform, a emissão padrão é um Peer e uma CA. Para uma rede de produção, os membros desejarão executar várias instâncias desses serviços para assegurar a disponibilidade. Por padrão, a IBM executa o Serviço de Solicitação para redes criadas pelo {{site.data.keyword.blockchainfull_notm}} Platform.  

## O {{site.data.keyword.blockchainfull_notm}} Platform

O {{site.data.keyword.blockchainfull_notm}} Platform fornece uma rede de blockchain altamente segura e com permissões na qual os membros autenticados podem definir ativos facilmente e criar as soluções de negócios para modificar e trocá-los. Inicializar uma rede de blockchain no nível de produção não é mais um processo tedioso e complicado. Com o {{site.data.keyword.blockchainfull_notm}} Platform você pode aproveitar uma estrutura de orquestração que permite **organizar seu consórcio em uma rede de blockchain ativa no momento do registro**. Fornecemos um conjunto de ferramentas apto para consórcio projetado para facilitar a reunião de várias instituições e a criação de uma rede controlada democraticamente. As tarefas de criação, governança e gerenciamento da rede se tornam intuitivas e transparentes por meio de um monitor de painel integrado e de utilitários provisionados. Em vez de passar pelo processo complicado de criar uma rede e implementar a governança, os membros do consórcio podem **focar na implementação de contratos inteligentes e transferência de ativos e informações**.      
{:shortdesc}

A **Alta Disponibilidade** para os componentes integrais da rede (peers, serviço de ordenação, Autoridade de Certificação, chaincode) elimina os efeitos paralisantes que podem surgir de pontos únicos de falha. Um monitor de painel integrado permite o fácil gerenciamento desses componentes e fornece um mecanismo poderoso para visualizar os ativos e contratos inteligentes.

A **modularidade** da arquitetura do Hyperledger Fabric V1.0 e a separação distinta de funções de rede fornecem uma infraestrutura que permite a escalabilidade e a adaptabilidade para uma ampla variedade de casos de uso. 

As verificações e os saldos que ocorrem em todo o ciclo de vida de uma transação asseguram resultados consistentes e completamente avaliados; e os livros razão permanecem constantemente sincronizados por meio de uma implementação do protocolo baseado em boato conhecido. A identidade e o controle de acesso são impingidos facilmente por meio das operações de **assinatura/verificação** que ocorrem permanentemente em toda a rede.  

É fornecido um **conjunto de ferramentas de governança** para permitir que os membros administrem e gerenciem as regras de negócios críticas para suas redes. Por exemplo, você pode desejar implementar uma política que defina quantos membros de uma rede devem concordar para que um novo membro participe. Ou, talvez haja um ativo que requeira aprovação de cada participante para que uma modificação ocorra. As regras de governança são fundamentais para qualquer tipo de rede de negócios e geralmente são extremamente elaboradas. O conjunto de ferramentas de governança (por exemplo, editores de política) simplifica bastante esse processo.

O serviço é executado em um ambiente **altamente seguro e isolado** sem acesso externo (incluindo acesso raiz) para componentes de rede. Os dados são criptografados em andamento e em repouso e os módulos de suporte de segurança de hardware permitem que chaves digitais sejam protegidas em conformidade com os regulamentos do segmento de mercado. A virtualização de hardware é usada para executar cada nó em um ambiente isolado, protegendo dessa forma outros nós na rede contra peers com chaincode potencialmente incorreto ou malicioso. Hashing, operações de assinatura/verificação e comunicações entre componentes são acelerados graças às implementações avançadas de criptografia.

Antes de continuar, vamos dar uma olhada em uma configuração simples dentro do {{site.data.keyword.blockchainfull_notm}} Platform. A **Figura 1** mostra um exemplo de uma rede de blockchain implementada que consiste em quatro membros (cada um possui dois peers), uma Autoridade de Certificação responsável por distribuir o material de identidade criptográfico e um serviço de solicitação que define políticas e participantes de rede. O canal azul contém todos os quatro membros de rede, ao passo que o canal amarelo é restrito a somente três membros: os Bancos B, C e D. Também podemos observar que o Banco A exerceu a função de inicializador de rede e que o Banco D existe somente como um membro no contexto do canal amarelo. Finalmente, um usuário final ou aplicativo em posse de um certificado x509 devidamente assinado é capaz de enviar chamadas para peers na rede. Conforme mencionado anteriormente, é bem possível que um usuário final não saiba sobre a existência da rede de blockchain.

![Rede de Blockchain](images/blockchain_network_2-01.png "Exemplo de rede de blockchain")

*Figura 2. Uma rede de blockchain de exemplo consiste em quatro membros que alavancam canais para isolar dados*

## Ofertas de Associação da {{site.data.keyword.IBM_notm}}

A tabela a seguir resume as opções de associação atuais e futuras. [Plano Corporativo](enterprise_plan.html) é a opção de associação que está atualmente disponível.

|       | Entrada      | [Corporativo](enterprise_plan.html)  | Corporativo Plus | Autogerenciado
| ------------------------- |--------------------------|-----|-----|------|
| **O que está incluído** | Níveis básicos de serviço | **Níveis de serviço avançados, prontos para produção corporativa** | Computação dedicada para desempenho e isolamento | Pilha de software para instalar em sua própria infraestrutura + conectar-se a uma rede hospedada nesse serviço de nuvem |
| **Política de faturamento** | Encargos por hora | **Assinatura mensal** | Assinatura mensal | Assinatura mensal |
| **Disponibilidade** | Em breve | **Disponível agora** | Em breve | Planejado para 2018 |

Inscreva-se agora para sua associação do [{{site.data.keyword.blockchain}} da {{site.data.keyword.IBM_notm}} ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](https://console.bluemix.net/catalog/services/blockchain?env_id=ibm:yp:us-south&taxonomyNavigation=apps).

## Suporte {{site.data.keyword.IBM_notm}}

A {{site.data.keyword.IBM_notm}} oferece suporte para as soluções {{site.data.keyword.blockchain}} implementadas pela {{site.data.keyword.IBM_notm}}. Para obter mais informações sobre o Suporte {{site.data.keyword.blockchainfull_notm}}, consulte [Obtendo suporte](ibmblockchain_support.html).

Para obter detalhes completos sobre todos os recursos e a funcionalidade do Hyperledger Fabric v1.0,
consulte a [Documentação do Hyperledger Fabric ![Ícone de link externo](images/external_link.svg "Ícone de link externo")](http://hyperledger-fabric.readthedocs.io/en/latest/).
