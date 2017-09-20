---

copyright:
  years: 2017
lastupdated: "2017-08-14"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Criando ou atualizando um canal

Os canais são um mecanismo muito poderoso para particionamento e isolamento de dados e eles fornecem a base principal
para privacidade de dados. Apenas membros do mesmo canal podem acessar os dados desse canal.
{:shortdesc}

Para garantir a segurança do canal, a política de atualização do canal é configurada para definir o número de operadores de canal que precisam concordar com a solicitação de criação ou atualização do canal antes de um canal ser criado ou atualizado. 

## Criando um canal
Clique no botão **Novo canal** na tela "Canais" de seu Monitor de Rede e conclua as etapas a seguir para enviar uma solicitação de criação de canal:  
1. Escolha um nome refletivo do objetivo de negócios do canal, inclua uma descrição, se desejar, e clique em **Avançar**. O nome do canal deve ser exclusivo em uma rede de Blockchain. Ele deve iniciar com uma letra e pode conter apenas caracteres minúsculos, números ou traços.  
  ![Criar canal 1](../images/create_channel.png "Painel 1 de Criar um canal")  
    
2. Convide qualquer combinação de seus membros de rede selecionando membros de rede e clicando no botão **Incluir membro**. Customize as permissões designando funções para cada um dos membros convidados e clique em **Avançar**.  
  ![Criar canal 2](../images/create_channel_2.png "Painel 2 de Criar um canal")  
  
    * Um operador de canal pode consultar ou atualizar o livro razão do canal. O operador de canal tem autoridade para **Aceitar** ou **Recusar** uma solicitação de criação do canal e para enviar uma solicitação de atualização do canal. Deve haver pelo menos um **Operador** em cada canal.  
    * Um escritor de canal pode atualizar o livro razão do canal.
    * Um leitor de canal pode apenas consultar o livro razão do canal.
  
3. Configure a política de atualização do canal selecionando o número de operadores de canal para aprovar a solicitação de atualização do canal e clique em **Enviar solicitação**.   
  ![Criar um canal 3](../images/create_channel_3.png "Painel 3 de Criar um canal")  

Os membros convidados receberão um e-mail de convite. Eles também podem localizar a solicitação com status "Votação Pendente" na tela **Notificações** do Monitor de Rede:  
* Membros que são convidados como operadores de canal podem clicar no botão **Revisar solicitação** para revisar a configuração do canal e, em seguida, **Aceitar** ou **Recusar** a solicitação de atualização do canal. Se a solicitação de atualização do canal obtiver votos **Aceitar** suficientes dos operadores do canal, seu status será mudado para "Voto Aceito". Qualquer operador de canal pode, então, clicar no botão **Enviar solicitação** para concluir a atualização do canal.  
* Membros que são convidados como escritores ou leitores de canal podem clicar no botão **Revisar solicitação** para revisar a configuração do canal.

Como um tipo especial de solicitação de atualização do canal, quando operadores de canal suficientes concordam com a solicitação de criação do canal e um operador de canal envia a solicitação, o novo canal é criado. Todos os membros do canal podem localizar o canal na tela "Canais" de seu Monitor de Rede.

## Atualizando um canal
Se você desejar modificar a configuração de um canal, por exemplo, incluir ou remover membros do canal ou mudar a política de atualização do canal, será possível enviar uma solicitação de atualização do canal. Na tela "Canais" de seu Monitor de Rede, localize o canal que você deseja modificar e selecione **Editar canal** na lista suspensa sob o cabeçalho **Ação**. Conclua as mesmas etapas à medida que você enviar uma solicitação de criação do canal para enviar uma solicitação de atualização do canal.

Todos os membros do canal receberão notificação por e-mail sobre a solicitação de atualização do canal:
* Os membros convidados recentemente receberão notificações por e-mail convidando-os a participar do canal. Eles também podem localizar a solicitação com o status "Votação Pendente" na tela **Notificações** de seu Monitor de Rede:  
    * Membros que são convidados como operadores de canal podem clicar no botão **Revisar solicitação** para revisar a configuração do canal e, em seguida, **Aceitar** ou **Recusar** a solicitação de atualização do canal. Se a solicitação de atualização do canal obtiver votos **Aceitar** suficientes dos operadores do canal, seu status será mudado para "Voto Aceito". Qualquer operador de canal pode, então, clicar no botão **Enviar solicitação** para concluir a atualização do canal.  
    * Membros que são convidados como escritores ou leitores de canal podem clicar no botão **Revisar solicitação** para revisar a configuração do canal.
* Os membros removidos recebem notificações por e-mail sobre a mudança do canal.
* Os operadores do canal existentes também recebem notificação por e-mail sobre a atualização do canal. Eles também podem localizar a solicitação com o status "Votação Pendente" na tela **Notificações** de seu Monitor de Rede para eles **Aceitarem** ou **Recusarem**.

Quando operadores de canal suficientes concordarem com a solicitação de atualização do canal e qualquer operador de canal enviar a solicitação, o canal será atualizado. Todos os membros do canal podem localizar o canal atualizado na tela "Canais" do Monitor de Rede.
