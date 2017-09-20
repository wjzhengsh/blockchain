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

# Creación o actualización de un canal

Los canales constituyen un mecanismo increíblemente potente para dividir y aislar datos y proporcionan la base principal de la privacidad de los datos.  Solo los miembros del mismo canal pueden acceder a sus datos. {:shortdesc}

Para garantizar la seguridad, su política de actualización se ha configurado de forma que define el número de operadores de canal que necesitan acordar la creación del canal o actualizar la solicitud antes de que se cree o actualice un canal. 

## Creación de un canal
Pulse el botón **Nuevo canal** en la pantalla "Canales" del Supervisor de red y realice los pasos siguientes para enviar una solicitud de creación de canal:  
1. Elija un nombre que refleje el objetivo empresarial del canal, añada una descripción si lo desea y pulse **Siguiente**. El nombre del canal debe ser exclusivo en una red de Blockchain. Debe empezar por una letra y puede contener solo caracteres en minúsculas, números o guiones.  
  ![Crear canal 1](../images/create_channel.png "Panel Crear un canal 1")  
    
2. Invite a una combinación de los miembros de la red seleccionando su **Nombre de empresa** y pulsando el botón **Añadir miembro**. Personalice los permisos mediante la asignación de roles a cada miembro invitado y pulse **Siguiente**.  
  ![Crear canal 2](../images/create_channel_2.png "Panel Crear un canal 2")  
  
    * Un operador de canal puede consultar o actualizar el libro mayor del canal. El operador de canal tiene permiso para **Aceptar** o **Rechazar** una solicitud de creación de canal y para enviar una solicitud de actualización. Debe haber como mínimo un **Operador** en cada canal.  
    * Un escritor de canal puede actualizar el libro mayor del canal. 
    * Un lector de canal puede consultar el libro mayor del canal. 
  
3. Configure la política de actualización de canal seleccionando el número de operadores de canal para aprobar la solicitud de actualización del canal y pulse **Enviar solicitud**.   
  ![Crear canal 3](../images/create_channel_3.png "Panel Crear un canal 3")  

Los miembros invitados recibirán un correo electrónico de invitación. También pueden encontrar la solicitud con el estado "Voto pendiente" en la pantalla **Notificaciones** del Supervisor de red:  
* Los miembros que están invitados como operadores de canal pueden pulsar el botón **Revisar solicitud** para repasar la configuración del canal y luego **Aceptar** o **Rechazar** la solicitud de actualización del canal. Si la solicitud de actualización del canal obtiene suficientes votos de **aceptación** de los operadores del canal, su estado cambiará a "Voto aceptado". Cualquier operador de canal puede pulsar el botón **Enviar solicitud** para finalizar la actualización del canal.  
* Los miembros que están invitados como escritores de canal pueden pulsar el botón **Revisar solicitud** para repasar la configuración del canal. 

Como tipo especial de solicitud de actualización de canal, cuando un número suficiente de operadores del canal aceptan la solicitud de creación y un operador de canal envía la solicitud, se creará el nuevo canal. Todos los miembros pueden encontrar el canal en la pantalla "Canales" del Supervisor de red. 

## Actualización de un canal
Si desea modificar la configuración de un canal, por ejemplo, añadir o eliminar miembros de canal o modificar su política de actualización, puede enviar una solicitud de actualización. En la pantalla "Canales" del Supervisor de red, localice el canal que desea modificar y seleccione **Editar canal** en la lista desplegable bajo la cabecera **Acción**. Lleve a cabo los mismos pasos del proceso de envío de una solicitud de creación de canal para enviar una solicitud de actualización.

Todos los miembros del canal recibirán una notificación por correo electrónico sobre la solicitud de actualización:
* Los miembros recién invitados reciben una notificación de correo electrónico con una invitación para unirse al canal. También pueden encontrar la solicitud con el estado "Voto pendiente" en la pantalla **Notificaciones** del Supervisor de red:  
    * Los miembros que están invitados como operadores de canal pueden pulsar el botón **Revisar solicitud** para repasar la configuración del canal y luego **Aceptar** o **Rechazar** la solicitud de actualización del canal. Si la solicitud de actualización del canal obtiene suficientes votos de **aceptación** de los operadores del canal, su estado cambiará a "Voto aceptado". Cualquier operador de canal puede pulsar el botón **Enviar solicitud** para finalizar la actualización del canal.  
    * Los miembros que están invitados como escritores de canal pueden pulsar el botón **Revisar solicitud** para repasar la configuración del canal. 
* Los miembros eliminados reciben una notificación por correo electrónico sobre el cambio en el canal.
* Los operadores de canal existentes también reciben una notificación por correo electrónico sobre la actualización del canal. También pueden encontrar la solicitud con el estado "Voto pendiente" en la pantalla **Notificaciones** del Supervisor de red donde podrán **Aceptar** o **Rechazar** la solicitud.

Cuando un número suficiente de operadores del canal aceptan la solicitud de actualización y algún operador del canal envía la solicitud, se creará el nuevo canal. Todos los miembros pueden encontrar el canal actualizado en la pantalla "Canales" del Supervisor de red. 
