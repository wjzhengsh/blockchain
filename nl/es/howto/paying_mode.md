---

copyright:
  years: 2018,2019
lastupdated: "2019-02-08"

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Modalidad de pago
{: #paying-mode}


***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***


{{site.data.keyword.blockchainfull}} Platform factura mensualmente cuotas de suscripción y cuotas por igual. Los miembros de la red pueden abonar su factura con sus cuentas de {{site.data.keyword.cloud_notm}} que contienen el espacio para crear la instancia de la red. Si lo desea, un miembro de la red puede hacerse cargo de la factura de todos los miembros de la red o incluso pagar la red completa. El pagador también puede transferir las facturas a otros miembros si es necesario.
{:shortdesc}

Dependiendo del plan de red que elija y de la cantidad de recursos que utilice, los pagos son diferentes. Para obtener más información sobre los precios, consulte [Tarifas](/docs/services/blockchain/howto/pricing.html#ibp-pricing).

Cuando varias organizaciones creen un consorcio, se recomienda que las organizaciones participantes utilicen una dirección de correo electrónico funcional para crear su red. En este caso, el acceso a la red no depende de la disponibilidad de ninguna persona individual.


## Requisitos previos
{: #paying-mode-prereq}

Cada miembro de red debe tener una cuenta de pago de {{site.data.keyword.cloud_notm}}, por ejemplo, una cuenta de **Pago según uso**, para poder crear instancias de servicio de {{site.data.keyword.blockchainfull_notm}} Platform en {{site.data.keyword.cloud_notm}}. Si no tiene una cuenta, [registre](https://console.bluemix.net/registration/) una y actualícela a una cuenta de pago. Para actualizar su cuenta a un tipo Pago según uso, vaya a **Gestionar** > **Facturación y uso** > **Facturación** en la consola de {{site.data.keyword.cloud_notm}}, y pulse **Añadir tarjeta de crédito**.


## Pago de sus propias cuotas
{: #paying-mode-pay-your-own}

Después de crear o de unir redes de blockchain en la plataforma {{site.data.keyword.blockchainfull_notm}}, se facturará a sus cuentas de {{site.data.keyword.cloud_notm}} la cuota de suscripción y las cuotas de los iguales.


## Pagar por otros miembros de la red
{: #paying-mode-pay-for-others}

Un miembro de la red se puede hacer cargo de las cuotas de uno o varios miembros de la red de la misma red de blockchain. Para pagar en esta modalidad, el pagador y los demás miembros de la red deben seguir los pasos siguientes.

1. El pagador crea distintos espacios de Cloud Foundry en {{site.data.keyword.cloud_notm}} para los miembros de cuya cuota se va a hacer cargo.
   1. Inicie una sesión en {{site.data.keyword.cloud_notm}}.
   2. En la barra de menús, pulse **Gestionar** > **Cuenta** > **Organizaciones de Cloud Foundry**.
   3. Pulse **Ver detalles** de la organización de Cloud Foundry en la que creará la red. Si no tiene una organización para la red, pulse el botón **Añadir una nueva organización de Cloud Foundry** para crear una.
   4. Pulse el botón **Añadir un espacio de Cloud Foundry** para crear un espacio para otro miembro de la red. Seleccione la región para el espacio, asígnele un nombre y pulse **Añadir**.  Tenga en cuenta que solo el creador del espacio puede acceder al espacio creado.  
   **Nota**: si el pagador se hace cargo de la cuota de más de un miembro, el pagador debe repetir este paso para crear distintos espacios para cada uno de los miembros cuya cuota va a pagar.
2. El pagador invita a otros miembros a la cuenta de {{site.data.keyword.cloud_notm}} del pagador y les otorga acceso a sus espacios específicos. Asegúrese de que cada miembro puede acceder solo a un espacio.
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
   **Nota**: si el pagador se hace cargo de la cuota de más de un miembro, el pagador debe repetir este paso para invitar a otros miembros y asignar acceso a cada uno de ellos.
3. El pagador crea una {{site.data.keyword.blockchainfull_notm}} Platform de la cadena de blockchain e invita a otros miembros a la red de blockchain en el supervisor de red de la red. Para obtener más información sobre cómo invitar a miembros de la red, consulte [Miembros](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members).
4. Luego cada miembro recibe una notificación por correo electrónico que contiene una invitación a unirse a la red. El miembro puede seguir los pasos siguientes para unirse a la red.
   1. Pulse el botón "Continuar" en la notificación por correo electrónico, lo que le llevará a la página del servicio blockchain de {{site.data.keyword.cloud_notm}}.
   2. Inicie una sesión en {{site.data.keyword.cloud_notm}} y asegúrese de utilizar la organización y el espacio de {{site.data.keyword.cloud_notm}} correctos, que ha añadido el pagador.
      1. En la consola de {{site.data.keyword.cloud_notm}}, pulse el avatar de perfil de la esquina superior derecha.
      2. Seleccione la cuenta del pagador en la lista desplegable **Cuenta**.  Tenga en cuenta que la cuenta y la organización donde se suministra la instancia del servicio blockchain será la que recibirá el cargo.  
   3. En el catálogo de {{site.data.keyword.cloud_notm}}, busque **Blockchain** y cree una instancia de servicio.
      1. En el campo **Nombre de servicio**, escriba en un nombre descriptivo que reconozca fácilmente en el futuro.
      2. Compruebe que la organización y el espacio son aquellos a los que ha añadido el pagador.
      3. Seleccione el plan de suscripción con el que va a crear su instancia de la red. El plan de pertenencia debe ser el mismo con el que el pagador crea la red.
      4. Pulse **Crear**.
   4. Después de crear la instancia del servicio blockchain, siga las instrucciones del asistente para unirse a la red.  Para obtener más información, consulte [Cómo unirse a una red](/docs/services/blockchain/get_start.html#getting-started-with-enterprise-plan-join-nw).

### Limitaciones conocidas
{: #paying-mode-limitation}
- Puesto que todos los miembros están dentro de la cuenta de {{site.data.keyword.cloud_notm}} del pagador, este tiene acceso a las instancias de blockchain de todos los miembros y los podría suplantar. Por lo tanto, esta modalidad de pago se utiliza preferentemente en entornos de prueba de concepto (PoC) o en los casos en que el pagador maneja toda la gestión de {{site.data.keyword.blockchainfull_notm}} Platform y solo se suministra una aplicación a los miembros.  
- Cualquier miembro de la organización de Cloud Foundry del pagador puede ver todos los espacios de la organización.  Sin embargo, no puede editar ni modificar las organizaciones porque no tiene acceso.  
- Tenga en cuenta que cualquier miembro de la organización de Cloud Foundry del pagador puede ver los ID de IBM Cloud de otros miembros de la misma organización de Cloud Foundry.  
- Después de añadir todos los miembros a la cuenta de {{site.data.keyword.cloud_notm}} del pagador y de asignarles acceso para suministrar instancias de blockchain y unirse a la red, el pagador también otorga a los miembros acceso para crear otros servicios, lo que podría ocasionar cargos adicionales. Todos los miembros podrían firmar un acuerdo con la confianza de que no crearán ningún servicio no autorizado en sus cuentas que ocasiones un coste extra a la cuenta del pagador.  

## Cómo dejar que un miembro pague por usted
{: #paying-mode-let-other-pay}

Como alternativa a la adición de cuentas de otros miembros a la cuenta del pagador, estos otros miembros pueden añadir la cuenta del pagador a sus cuentas como **Gestor de facturación**. Para pagar en esta modalidad, el pagador y los demás miembros de la red deben seguir los pasos siguientes.

1. El pagador crea una {{site.data.keyword.blockchainfull_notm}} Platform de la cadena de blockchain con la cuenta del pagador.
2. Otros miembros añaden la cuenta del pagador a su cuenta de {{site.data.keyword.cloud_notm}} como **Gestor de facturación**.
  1. En la barra de menús de la parte superior de la consola de {{site.data.keyword.cloud_notm}}, pulse **Gestionar** > **Cuenta** > **Usuarios**.
  2. En la página **Usuarios**, pulse el botón **Invitar usuarios**.
  3. Añada la cuenta del pagador bajo el campo **Dirección de correo electrónico**.
  4. En la sección **Acceso de Cloud Foundry**, elija la organización a la que desea añadir la cuenta del pagador en la lista desplegable.
  5. Seleccione **Gestor de facturación** como **Roles de la organización**.
  6. Configure otros campos como corresponda y luego pulse **Invitar usuarios**.  
3. El pagador invita a otros miembros a la red de blockchain en el supervisor de red de la red. Para obtener más información sobre cómo invitar a miembros de la red, consulte [Miembros](/docs/services/blockchain/v10_dashboard.html#ibp-dashboard-members).
4. Luego cada miembro recibe una notificación por correo electrónico que contiene una invitación a unirse a la red. A continuación, el miembro puede unirse a la red. Para obtener más información, consulte el **paso 4** de la sección [Cómo pagar por otros miembros de la red](/docs/services/blockchain/howto/paying_mode.html#paying-mode-pay-for-others).

### Limitación conocida
{: #paying-mode-let-other-pay-limitation}

Después de añadir la cuenta de {{site.data.keyword.cloud_notm}} del pagador como gestor de facturación a las cuentas de otros miembros y de que los miembros adquieran otros servicios utilizando su cuenta, el pagador también tiene que cubrir esos cargos. Todos los miembros podrían firmar un acuerdo con la confianza de que no crearán ningún servicio no autorizado en sus cuentas que ocasiones un coste extra a la cuenta del pagador.  


## Transferencia de facturas a otros miembros
{: #paying-mode-transfer-billing}

Aunque los miembros de la red pueden tener un miembro que pague por ellos, luego otros miembros pueden hacerse cargo de las facturas. Puede eliminar la cuenta del pagador de sus cuentas para que el pagador deje de pagar por usted.

1. En la barra de menús de la parte superior de la consola de {{site.data.keyword.cloud_notm}}, pulse **Gestionar** > **Cuenta** > **Organizaciones de Cloud Foundry**.
2. Pulse la organización a la que desea añadir la cuenta del pagador.
3. Vaya al separador **Usuario**.
4. Busque la cuenta del pagador en la tabla de usuarios y pulse el enlace **Eliminar de organización**.

Después de eliminar la cuenta del pagador de su cuenta, los cargos de iguales y de pertenencia a la red blockchain se cargarán a su cuenta.
