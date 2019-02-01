---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Migrando do Starter Plan para o Enterprise Plan
{: #migrate_starter_to_enterprise}


***[Esta página é útil? Diga-nos.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


O [Starter Plan](/docs/services/blockchain/starter_plan.html) do {{site.data.keyword.blockchainfull}} Platform oferece um ambiente de teste e desenvolvimento para executar seu PoC, demos e experimentar seu chaincode e aplicativos. Quando você estiver pronto para ir para um ambiente de produção, poderá migrar de uma rede do Starter Plan para uma rede do [Enterprise Plan](/docs/services/blockchain/enterprise_plan.html).
{:shortdesc}

As redes do Enterprise Plan oferecem os recursos prontos para produção a seguir para suportar sua carga de trabalho de produção:

- Redes de blockchain disponíveis em locais globais adicionais às redes do Starter Plan.
- Nenhum limite de armazenamento, que é de 20 GB para o Starter Plan.
- Gerenciamento aprimorado de CPU e RAM para assegurar que todas as redes sejam executadas sem problemas.
- Segurança aprimorada, incluindo as funções a seguir:
  - O Secure Service Container (SSC) assegura que a imagem do blockchain não possa ser violada e carregada em um determinado momento e que o código do dispositivo e os dados sejam protegidos de forma confidencial tanto em andamento quanto em repouso.
  - [Hardware secure module (HSM)](/docs/services/blockchain/glossary.html#hsm) para gerenciamento e armazenamento de chave.
  - Criptografia de disco pervasiva.
- Alta Disponibilidade, Recuperação de Desastre, Tolerância a Falhas de Travamento e upgrades contínuos.
- Suporte avançado opcional.

## Considerações
{: #migrate_starter_to_enterprise_considerations}

Antes de migrar da rede do Starter Plan para uma rede do Enterprise Plan, é possível ler as considerações a seguir.

- **Precificação:** a taxa mensal de sua organização para usar uma rede do Enterprise Plan inclui a taxa de associação de US$ 1000 por instância e a taxa de US$ 1000 por peer de acordo com o peer. Para obter mais informações, consulte
[Precificação do Enterprise Plan](/docs/services/blockchain/howto/pricing.html#enterprise-plan-pricing).
- **Versão do Hyperledger Fabric:** as redes do Enterprise Plan são executadas no Hyperledger Fabric v1.1. As redes do Starter Plan são executadas no Fabric v1.2. Um componente como [Dados Privados](https://hyperledger-fabric.readthedocs.io/en/release-1.2/private-data/private-data.html), como um exemplo --- significando um chaincode desenvolvido para usar Dados Privados --- que trabalha com uma rede do Starter Plan, não funcionará no Enterprise Plan.
- **Recursos afetados:** Chaincode (contratos inteligentes), definições da rede de negócios, aplicativos clientes. Novamente, esteja ciente de que seu chaincode está alavancando um componente do Fabric v1.2 ou uma funcionalidade que não é compatível com redes v1.1.
- **Tempo necessário:** Levará pelo menos metade de um dia para migrar uma rede básica do Starter Plan para o Enterprise Plan.
- **Os dados existentes do livro-razão** não podem ser movidos das redes do Starter Plan para as redes do Enterprise Plan porque não é apropriado que existam dados de teste em um ambiente de produção.

**Nota:** uma rede *básica* inclui duas organizações com dois peers, um único canal e um único arquivo de chaincode ou de Business Network Archive (`.bna`). O tempo real para migrar pode variar, dependendo do tamanho e da complexidade dos componentes de rede necessários na rede do Enterprise Plan.

## Lista de verificação de migração
{: #migrate_starter_to_enterprise_checklist}

Há uma série de tarefas necessárias para preparar a movimentação para uma rede do Enterprise Plan por meio de uma rede do Starter. É possível localizar instruções detalhadas em seções posteriores, mas aqui está um resumo:

- Criar uma rede do Enterprise Plan.
- Se você desenvolveu um `.bna` usando o Hyperledger Composer, localize e migre o arquivo `.bna`.
- Recriar a configuração desejada de organizações e peers por meio da rede de teste do Starter Plan.
- Identificar qual chaincode, escrito em Go ou Node, será executado na rede do Enterprise Plan.
- Atualizar aplicativos clientes com as novas informações do terminal de API para a rede do Enterprise Plan.

### Criar uma rede do Enterprise Plan
{: #migrate_starter_to_enterprise_create_network}

É necessário criar uma rede do Enterprise Plan antes de migrar. Para obter mais informações, consulte [Criando uma rede do Enterprise Plan](/docs/services/blockchain/get_start.html#creating-a-network).

### Migrar um arquivo  ` .bna `
{: #migrate_starter_to_enterprise_bna}

**Nota:** será possível ignorar essa etapa se você não usar um arquivo `.bna` na rede do Starter Plan.

Se você usou o Hyperledger Composer para definir uma rede de negócios e implementou um arquivo `.bna` na rede do Starter Plan, será possível implementar o mesmo arquivo `.bna` na rede do Enterprise Plan.

- Se você tiver o archive de rede de negócios (`.bna`), siga as instruções em [Implementando uma rede de negócios no Enterprise Plan](/docs/services/blockchain/develop_enterprise.html).
- Se você não tiver o seu arquivo `.bna`, use o comando `composer network download` para recuperá-lo da instância do Starter Plan. Para obter mais informações sobre o comando `composer network download`, veja [documentação da linha de comandos do Hyperledger Composer![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger.github.io/composer/latest/reference/commands){:new_window}. Em seguida, será possível seguir as instruções em [Implementando uma rede de negócios no Enterprise Plan](/docs/services/blockchain/develop_enterprise.html).

### Recrear configuração de rede
{: #migrate_starter_to_enterprise_config_network}

É possível recriar a configuração de organizações (membros), canais e peers da rede do Starter Plan na rede do Enterprise Plan. É possível usar a IU do Monitor de rede para recriar esses recursos de rede convidando as organizações apropriadas (observe que não será possível **alternar** as organizações como no Starter), criar canais e peers (novamente, as organizações convidadas terão que criar seus próprios peers).

1. Efetue login na rede do Enterprise Plan no {{site.data.keyword.cloud_notm}} e entre no Monitor de rede.
2. Recrie as organizações (membros) na tela "Membros", recrie os canais na tela "Canais" e crie novamente os peers na tela "Visão geral". Para obter informações adicionais sobre como criar recursos de rede, consulte [Usando o Monitor de Rede](/docs/services/blockchain/v10_dashboard.html#overview).
3. Configure os canais incluindo membros e configurando políticas de canal da mesma maneira que na rede do Starter Plan.

**Nota:** para atingir Alta disponibilidade, deve-se criar pelo menos dois peers para sua organização, associá-los ao mesmo canal e codificar os aplicativos clientes adequadamente para alternar de um peer para o outro quando necessário.

### Migrar chaincode
{: #migrate_starter_to_enterprise_cc}

O Chaincode é desenvolvido externamente em seu ambiente local e é chamado por seus aplicativos clientes. Para instalar e instanciar o chaincode, que foi testado na rede do Starter Plan, em peers selecionados na rede do Enterprise Plan, siga as instruções em [Instalando, instanciando e atualizando um chaincode](/docs/services/blockchain/howto/install_instantiate_chaincode.html#installchaincode).

### Atualizar aplicativos clientes
{: #migrate_starter_to_enterprise_app}

É necessário atualizar os aplicativos clientes existentes, que são testados na rede do Starter Plan, com novas informações do terminal de API para a rede do Enterprise Plan. Para obter mais informações, consulte [Recuperando credenciais de rede e o perfil de conexão](/docs/services/blockchain/get_start.html#retrieving-network-credentials-and-connection-profile).

Com respeito à Alta disponibilidade, é responsabilidade dos aplicativos clientes detectar quando um peer não está respondendo e rotear transações para um peer diferente.

## O que fazer com as redes existentes do Starter Plan
{: #migrate_starter_to_enterprise_existing_network}

É possível continuar usando a rede existente do Starter Plan como um ambiente de simulação para incubar novos projetos e testar mudanças no chaincode existente. Além disso, é possível manter os dados do livro-razão de teste na rede do Starter Plan, que não são migrados para a rede do Enterprise Plan.

Não exclua a rede do Starter até que conclua todos os testes e verifique se tudo está funcionando corretamente. No entanto, uma vez que não precise mais da rede do Starter e dos respectivos dados do livro-razão, será possível excluir com segurança a rede. Para obter mais informações, consulte [Excluindo ou saindo de uma rede](/docs/services/blockchain/get_start_starter_plan.html#deleting-or-leaving-a-network).
