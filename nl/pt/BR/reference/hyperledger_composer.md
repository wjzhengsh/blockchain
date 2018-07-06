---

copyright:
  years: 2017, 2018
lastupdated: "2018-06-14"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Editor do Hyperledger
{: #hyperledger-composer}

{{site.data.keyword.blockchainfull}} Platform: Develop é construído no conjunto de ferramentas do Hyperledger Composer de software livre, um dos projetos dentro do Hyperledger Project da Linux Foundation. O Hyperledger Composer permite que arquitetos e desenvolvedores criem rapidamente soluções do {{site.data.keyword.blockchain}} com APIs de REST que expõem a lógica de negócios para aplicativos da web ou móveis, bem como a integração de blockchain com sistemas corporativos de registro existentes.
{:shortdesc}

O Hyperledger Composer é composto de um tempo de execução, uma interface de linha de comandos, um servidor REST, um conector LoopBack, uma interface com o usuário do Playground, um gerador de código do Yeoman e os plug-ins editor do VSCode e do Atom. Para obter mais informações sobre o Hyperledger Composer, veja a [Documentação do Hyperledger Composer ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger.github.io/composer/latest/introduction/introduction.html)


## Redes de negócios

O Hyperledger Composer permite que você rapidamente modele o seu negócio atual de rede, que contém os seus ativos existentes e as transações relacionadas a eles; os recursos são bens tangíveis ou intangíveis, serviços ou propriedade. Como parte de seu modelo de rede de negócios, você define as transações que podem interagir com ativos. As redes de negócios também incluem os participantes que interagem com elas, cada um dos quais pode ser associado a uma identidade exclusiva, nas múltiplas redes de negócios.

Para obter mais informações sobre a construção de redes de negócios, veja [Desenvolvendo redes de negócios](./develop.html) ou a [Documentação do Hyperledger Composer ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger.github.io/composer/latest/introduction/introduction.html).

## Consultas

Consultas são usadas para retornar dados sobre o estado geral do blockchain. As consultas são definidas em uma rede de negócios e podem incluir parâmetros de variáveis para customização simples. Usando consultas, os dados podem ser facilmente extraídos de sua rede do {{site.data.keyword.blockchain}}. Consultas são enviadas usando a [API do Hyperledger Composer![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger.github.io/composer/latest/api/api-doc-index).

Para obter mais informações sobre como usar consultas em sua rede de negócios, veja [Consultando e filtrando dados de rede de negócios ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger.github.io/composer/business-network/query).

## Identidades e placas de rede de negócios

Dentro de uma rede de negócios, os participantes estão associados com uma identidade. Placas de rede de negócios são uma combinação de uma identidade, um perfil de conexão e metadados e são usadas para se conectar a uma rede de negócios e demonstrar identidade para transações. Placas de rede de negócios simplificam o processo de conexão com uma rede de negócios e estendem o conceito de uma identidade fora da rede de negócios para uma 'carteira' de identidades, cada uma associada a uma rede de negócios e um perfil de conexão específicos.

Para obter mais informações sobre identidades e placas de rede de negócios, veja [Participantes e identidades ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger.github.io/composer/managing/participantsandidentities).

## Servidor de REST do Hyperledger Composer

O servidor REST do Hyperledger Composer gera automaticamente uma API de REST do Swagger para uma rede de negócios. O servidor REST é baseado em LoopBack e converte o modelo de rede de negócios em uma definição de API Aberta. No tempo de execução o servidor REST implementa Criar, Ler, Atualizar e Excluir suporte para ativos e participantes e permite que as transações sejam enviadas para processamento ou recuperadas com consultas.

Para obter mais informações sobre o servidor REST do Hyperledger Composer, veja [Gerando uma API de REST ![Ícone de link externo](../images/external_link.svg "Ícone de link externo")](https://hyperledger.github.io/composer/integrating/getting-started-rest-api).
