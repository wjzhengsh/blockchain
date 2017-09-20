---

copyright:
  years: 2017
lastupdated: "2017-07-19"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}


# Renúncia de Responsabilidade
{: #etn_overview}

**ATENÇÃO:** deve-se revisar as informações a seguir antes de usar quaisquer planos do {{site.data.keyword.blockchainfull}}.

## Instrução de suporte IBM

A longa história de liderança da IBM em inovação continua com os planos de oferta do {{site.data.keyword.blockchainfull_notm}} no {{site.data.keyword.Bluemix_notm}}. {{site.data.keyword.blockchain}} é uma tecnologia em rápida progressão que foi projetada para revolucionar o setor financeiro, as cadeias de suprimento locais e globais e o apoio logístico em qualquer número de espaços de negócios. Por meio de vários programas de adoção antecipada, clientes e parceiros de negócios IBM têm conduzido o blockchain ativamente como uma solução industrial. O {{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} é um desses programas. **Como com qualquer nova tecnologia, os usuários do {{site.data.keyword.blockchainfull_notm}} on {{site.data.keyword.Bluemix_notm}} devem estar cientes do potencial para mudanças rápidas e fundamentais**.  
{:shortdesc}

A arquitetura por trás do {{site.data.keyword.blockchainfull_notm}} é o projeto Hyperledger Fabric da Linux Foundation. Cada contribuição da comunidade de software livre melhora o Hyperledger Fabric, mas pode apresentar desafios de adoção. **A IBM tem reservas com relação à definição ou troca de ativos financeiros ou de quaisquer ativos de valor, diretamente em qualquer rede de blockchain do Hyperledger Fabric**.  

## Instrução de software livre

Os planos de oferta do {{site.data.keyword.blockchainfull_notm}} no {{site.data.keyword.Bluemix_notm}} são construídos em cima da pilha do Hyperledger Fabric V1.0 da Linux Foundation. Os membros do Hyperledger Project, incluindo a IBM, continuam contribuindo com vários subprojetos sob a coordenação do Hyperleder. Todas as contribuições são revisadas, avaliadas e testadas pela comunidade. 

O Hyperledger Fabric anunciou que atingiu o *Status de Nível de Produção* para a V1.0 em 11 de julho de 2017. 

## Instrução de suporte de chaincode

As práticas de codificação a seguir NÃO são suportadas em redes do {{site.data.keyword.blockchainfull_notm}}:

1. Usar matrizes associativas com iteração (a ordem é aleatória em Go).
2. Ler uma lista de itens de uma tabela de KVS (a ordem não é garantida).
3. Gravar chaincode inseguro para encadeamento (consulta e chamada podem ser chamadas em paralelo).
4. Substituir memória ou armazenamento em cache global para variáveis de estado do livro razão no chaincode.
5. Acessar serviços externos, como bancos de dados, diretamente de chaincode.
6. Usar bibliotecas ou variáveis globais que podem introduzir o não determinismo (como usar "aleatório" ou "tempo").  

Além disso, não é recomendado gravar chaincode não determinístico, o que apresenta risco à consistência e à integridade dos dados. Deve ser observado que a arquitetura do Hyperledger Fabric foi projetada para combater chaincode não determinístico por meio de uma série verificações de endosso e validação, no entanto, ainda é altamente recomendável que você codifique funções determinísticas que não sejam dependentes de variáveis globais não estáticas (por exemplo, tempo).  
