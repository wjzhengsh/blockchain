---

copyright:
  years: 2017, 2018
lastupdated: "2018-03-16"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Pago de la red

**Nota**: el Plan inicial de la plataforma {{site.data.keyword.blockchainfull}} es gratuito en su versión Beta.

La plataforma {{site.data.keyword.blockchainfull_notm}} factura mensualmente cuotas de suscripción y cuotas por igual. Dependiendo del plan de red que elija y de la cantidad de recursos que utilice, los pagos son diferentes.  Si tomamos como ejemplo el Plan empresarial, los miembros de la red deben pagar 1.000 $ al mes como cuota de suscripción y otros 1.000 $ al mes por cada uno de los iguales de la red. La cuota mensual se factura prorrateada por día. Por ejemplo, un miembro (cuota de suscripción asociada de 1.000 $) con dos iguales (la cuota por igual es de 1.000 $ X 2 iguales) pagaría 3.000 $ al mes. Si el mes tiene 30 días, el miembro paga 100 $ (3.000 $/30) al día.

Los miembros de la red pueden abonar su factura con sus propias cuentas de IBM Cloud que contienen el espacio para crear la instancia de la red. Si lo desea, un miembro de la red puede hacerse cargo de la factura de todos los miembros de la red y pagar la red completa.


## Pago de sus propias cuotas
Se facturará a sus cuentas de {{site.data.keyword.cloud_notm}} a diario la cuota de suscripción y las cuotas por igual.


## Pago de toda la red
Un miembro de la red se puede hacer cargo de las cuotas de todos los miembros de la red.  Para pagar en esta modalidad de un solo pagador, el pagador y los demás miembros de la red deben seguir los pasos siguientes.

### Requisitos previos
Cada miembro de la red debe tener una cuenta de {{site.data.keyword.cloud_notm}}. [Registre](https://console.bluemix.net/registration/) una si no la tiene.

### Procedimientos
1. El pagador crea distintos espacios de Cloud Foundry en {{site.data.keyword.cloud_notm}} para cada usuario que será invitado a la red:
   1. Inicie una sesión en {{site.data.keyword.cloud_notm}}.
   2. En la barra de menús, pulse **Gestionar** > **Cuenta** > **Organizaciones de Cloud Foundry**.
   3. Pulse **Ver detalles** de la organización de Cloud Foundry en la que creará la red.  Si no tiene una organización para la red, pulse el botón **Añadir una nueva organización de Cloud Foundry** para crear una.
   4. Pulse el botón **Añadir un espacio de Cloud Foundry** para crear un espacio para un miembro de la red.  Seleccione la región para el espacio, asígnele un nombre y pulse **Añadir**.  Tenga en cuenta que solo el creador del espacio puede acceder al espacio creado.
   5. Repita el paso 4 para crear espacios separados para todos los usuarios que serán invitados a la red.
2. El pagador invita a otros usuarios a la cuenta de {{site.data.keyword.cloud_notm}} del pagador y les otorga acceso al espacio específico.  Cada usuario tiene acceso a un solo espacio.
   1. En la barra de menús, pulse **Gestionar** > **Cuenta** > **Usuarios**.  
   2. Pulse **Invitar a usuarios** y asigne acceso de usuario:
      1. Especifique la dirección de correo electrónico de un solo usuario que va a invitar a su organización de Cloud Foundry.
      2. En la sección **Acceso a Cloud Foundry**, realice las selecciones siguientes:
         - **Organización**: la organización de Cloud Foundry en la que se va a crear la red.
         - **Roles de organización**: auditor.
         - **Región**: la región en la que ha creado el espacio para este usuario.
         - **Espacio**: el espacio que ha creado para este usuario.
         - **Roles del espacio**: desarrollador.
      3. Pulse **Invitar a usuarios**.
   3. Repita los pasos para invitar a usuarios y asignar acceso de usuario a cada usuario.
3. En el panel de control de la plataforma {{site.data.keyword.blockchainfull_notm}}, el pagador invita a los usuarios a la red blockchain. Para obtener más información sobre cómo invitar a miembros de la red, consulte [Miembros](https://console.bluemix.net/docs/services/blockchain/v10_dashboard.html#members).
4. Luego cada usuario recibe una notificación por correo electrónico que contiene una invitación a unirse a la red.  El usuario puede seguir los pasos siguientes para unirse a la red.
   1. Pulse el botón "Continuar" en la notificación por correo electrónico, lo que le llevará a la página del servicio blockchain de {{site.data.keyword.cloud_notm}}.
   2. Asegúrese de utilizar la organización y el espacio de {{site.data.keyword.cloud_notm}} correctos.
      1. Inicie una sesión en {{site.data.keyword.cloud_notm}} y pulse el avatar de perfil de la parte superior derecha.
      2. Seleccione la cuenta del pagador en la lista desplegable **Cuenta**.  Tenga en cuenta que la cuenta y la organización donde se suministra la instancia del servicio blockchain será la que recibirá el cargo.  
   4. En la página del servicio blockchain, cree su instancia del servicio blockchain:
      1. En el campo **Nombre de servicio**, escriba en un nombre descriptivo que reconozca fácilmente en el futuro.
      2. Compruebe que la organización y el espacio son aquellos a los que le ha invitado el pagador.
      3. Seleccione el plan de suscripción con el que va a crear su instancia de la red.
      4. Pulse **Crear**.
   5. Después de crear la instancia del servicio blockchain, siga las instrucciones del asistente para unirse a la red.  Para obtener más información, consulte [Cómo unirse a una red](https://console.bluemix.net/docs/services/blockchain/get_start.html#joining-a-network).

### Limitaciones conocidas
- Puesto que todos los miembros están dentro de la cuenta de {{site.data.keyword.cloud_notm}} del pagador, este tiene acceso a las instancias de blockchain de todos los miembros y los podría suplantar.  Por lo tanto, esta modalidad de pago se utiliza preferentemente en entornos de prueba de concepto (POC) o en los casos en que el pagador maneja toda la gestión de la plataforma {{site.data.keyword.blockchainfull_notm}} y solo se suministra una aplicación a los miembros.  
- Después de añadir todos los miembros a la cuenta de {{site.data.keyword.cloud_notm}} del pagador y de asignarles acceso para suministrar instancias de blockchain y unirse a la red, el pagador también otorga a los miembros acceso para crear otros servicios, lo que podría ocasionar cargos adicionales.  
- Cualquier miembro de la organización de Cloud Foundry del pagador puede ver todos los espacios de la organización.  Sin embargo, no puede editar ni modificar las organizaciones porque no tiene acceso.
