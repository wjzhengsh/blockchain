---

copyright:
  years: 2017, 2018
lastupdated: "2019-03-05"

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:pre: .pre}

# Precios
{: #ibp-pricing}


Esta guía le ayuda a comprender los precios de los planes de suscripción a {{site.data.keyword.blockchainfull}} Platform, y cuánto se pagará a medida que desarrolle y haga crecer la red blockchain.  
{:shortdesc}

{{site.data.keyword.blockchainfull_notm}} Platform carga mensualmente las tarifas de suscripción y de iguales a las organizaciones que crean redes de blockchain. Las tarifas son diferentes dependiendo del plan de suscripción que elija y de los recursos de red que utilice la red. La tabla siguiente muestra una visión general de los precios de la plataforma de {{site.data.keyword.blockchainfull_notm}}.

| Elementos de precios | Coste del Plan inicial al mes | Coste del Plan empresarial al mes |
|-----|-----|-----|
| Cuota de suscripción | 250 dólares | 1000 dólares |
| Cuota de iguales | 125 dólares | 1000 dólares |

*Figura 1. Visión general de los precios de {{site.data.keyword.blockchainfull_notm}} Platform*

La cuota mensual se factura prorrateada por día. Por ejemplo, un miembro (cuota de suscripción asociada de 1.000 $) con dos iguales (la cuota por igual es de 1.000 $ X 2 iguales) pagaría 3.000 $ al mes. Si el mes tiene 30 días, el miembro paga 100 $ (3.000 $/30) al día. Para obtener más información sobre cómo pagar para las redes, consulte [Modalidad de pago](/docs/services/blockchain/howto/paying_mode.html#paying-mode).


## Componentes básicos de la red
{: #ibp-pricing-components}

Para entender los precios, necesitamos empezar con una introducción a los componentes básicos de una red. La plataforma de {{site.data.keyword.blockchainfull_notm}} permite la creación de una red blockchain que se basa en Hyperledger Fabric. En un nivel alto, una red blockchain de Fabric consta de los siguientes componentes básicos:

-	**Organizaciones**: Cualquier entidad que necesite mantener una copia del libro mayor de blockchain y que necesite validar las transacciones. Puede haber varias organizaciones de blockchain para una sola compañía.
-	**Iguales**: El nodo asociado con una organización que contiene el libro mayor de blockchain y que valida transacciones. Los iguales están asociados con una organización de blockchain individual.
-	**Servicio de ordenación**: Compuesto por un clasificador único (SOLO) o una recopilación de clasificadores. El servicio de ordenación secuencia transacciones, crea bloques y envía bloques a iguales para su validación.
-	**Entidad emisora de certificados (CA)**: Emite certificados digitales para fines de identificación a cualquier componente de red interactivo.

La plataforma de {{site.data.keyword.blockchainfull_notm}} ofrece dos planes de suscripción, el **Plan inicial** y el **Plan empresarial**, que puede elegir en {{site.data.keyword.cloud_notm}}. Ambos planes le permiten crear organizaciones y le proporcionan una entidad emisora de certificados. Los planes difieren entre iguales, CA y el servicio de ordenación.

El Plan empresarial le proporciona CA e iguales altamente disponibles con un servicio de ordenación tolerante a errores de bloqueo. El Plan inicial no tiene opciones de alta disponibilidad y utiliza un servicio de ordenación básico SOLO. Por este motivo, el Plan inicial está diseñado para ser el punto de entrada de la plataforma de {{site.data.keyword.blockchainfull_notm}} para los entornos de desarrollo, de pruebas y de pruebas de concepto. El Plan empresarial es la opción para las redes que están listas para los entornos piloto y de producción.

## Elementos clave de precios
{: #ibp-pricing-key-elements}

Tanto el Plan inicial como el Plan empresarial tienen dos elementos de precios:

- **Cuota de suscripción**: Cubre la creación de organizaciones, el acceso al servicio de ordenación y CA, y se carga con una base **por instancia**. Incluida en este elemento de precios, {{site.data.keyword.blockchainfull_notm}} Platform maneja el servicio de ordenación y la CA en nombre de la red. Esta cuota es necesaria para tener acceso a una red creada en la plataforma de {{site.data.keyword.blockchainfull_notm}}.

  -	El Plan inicial permite organizaciones *ilimitadas* por suscripción y la capacidad de conmutar entre las organizaciones del Supervisor de red. Dado que el Plan inicial está diseñado para entornos de desarrollo, de pruebas y de POC, puede simular en los entornos de varias organizaciones. **Tenga en cuenta** que el almacenamiento en red total está limitado a 20 GB, incluidos los componentes, el código de encadenamiento y los datos del libro mayor. Las organizaciones simuladas comparten los 20 GB de almacenamiento en la red blockchain.

  -	Empresarial permite una organización única por suscripción. Dado que Empresarial está diseñado para entornos piloto y de producción, se le enlazará a la organización específica.

-	**Cuota por iguales**: Cubre los iguales de una organización y se carga en una base **por igual**. Esta cuota solo es necesaria si desea que un igual mantenga una copia del libro mayor y que valide las transacciones.

### Ejemplo de cuota de suscripción
{: #ibp-pricing-example}

La Figura 2 muestra un ejemplo de instancias de red, que pueden ayudar a comprender la cuota de suscripción. En la figura, la cuenta particular de {{site.data.keyword.cloud_notm}} proporciona tres instancias de red: una instancia del Plan empresarial con el nombre de *Blockchain-11* y dos instancias del Plan inicial con el nombre de *Blockchain-cz* y *Blockchain-da*. Cada instancia requiere su propio servicio de ordenación y CA. En este caso, esta cuenta particular de {{site.data.keyword.cloud_notm}} necesita pagar tres cuotas de suscripción, una para cada instancia de red.

![Instancias de red blockchain](../images/ibp_instance_example.png "Instancias de red blockchain")  
*Figura 2. Instancias de red blockchain*


## Tarifas del Plan inicial
{: #ibp-pricing-starter-pricing}

Las redes del Plan inicial se suministran con una configuración predeterminada de dos organizaciones y un igual por organización, para un total de 500 dólares al mes. Con la configuración predeterminada, no hay necesidad inmediata de manipular el entorno para empezar. Puede añadir más organizaciones de forma gratuita, y puede añadir más iguales a sus organizaciones con un cargo de 125 dólares al mes por igual. Su factura se refleja en la Figura 3 si crea o se une a una red de Plan inicial con la configuración predeterminada.

| Componentes de tarifas | Coste al mes |
|-----|----------------|
| Cuota de suscripción | 250 dólares |
| Cuota de iguales | 125 dólares |
| Cuota de iguales | 125 dólares |
| Fin del cargo mensual | 500 dólares |

*Figura 3. Cargo de una red predeterminada del Plan inicial*

### Ejemplo de tarifas del Plan inicial
{: #ibp-pricing-starter-plan-pricing-example}

#### Iguales adicionales
{: #ibp-pricing-additional-peers}

El Plan inicial no restringe el número de iguales que puede añadir a la red. Si añade, por ejemplo, dos iguales a la red predeterminada del Plan inicial, uno para cada organización, aumentará su factura en 250 dólares al mes. Su factura se refleja en la Figura 4 si añade dos iguales adicionales al principio del primer mes al crear o unirse a una red del Plan inicial.

| Componentes de tarifas | Coste al mes |
|-----|----------------|
| Red 1 de la cuota de suscripción | 250 dólares |
| Cuota de iguales | 125 dólares |
| Cuota de iguales | 125 dólares |
| Cuota de iguales | 125 dólares |
| Cuota de iguales | 125 dólares |
| Total | 750 dólares |
| Fin del primer cargo mensual | 750 dólares |

*Figura 4. Cargo de una red predeterminada del Plan inicial con dos iguales adicionales*

#### Redes adicionales
{: #ibp-pricing-additional-networks}
El Plan inicial tampoco restringe el número a instancias de red que se pueden suministrar. Si suministra una segunda instancias de red del Plan inicial, deberá pagar una segunda cuota de suscripción y un segundo conjunto de cuotas de iguales asociado con la segunda red. Por lo tanto, aumentará su factura en 500 dólares al mes por utilizar la configuración de red predeterminada. Puede que se encuentre en el caso en el que necesite redes iniciales adicionales para los entornos de desarrollo, de prueba y de pruebas de concepto aislados entre sí. Un ejemplo sería cuando desee hacer que sus ingenieros de calidad realicen pruebas funcionales significativas fuera de su entorno de desarrollo.

Su factura se refleja en la Figura 5 si añade una red adicional al principio del primer mes al crear o unirse a una red del Plan inicial.

| Componentes de tarifas | Coste al mes |
|-----|----------------|
| Red 1 de la cuota de suscripción | 250 dólares |
| Cuota de iguales | 125 dólares |
| Cuota de iguales | 125 dólares |
| Red 2 de la cuota de suscripción | 250 dólares |
| Cuota de iguales | 125 dólares |
| Cuota de iguales | 125 dólares |
| Fin del primer cargo mensual | 1000 dólares  |

*Figura 5. Cargo de dos redes predeterminadas del Plan inicial*

#### Eliminación de iguales
{: #ibp-pricing-removing-peers}

También puede eliminar un igual de la configuración de red predeterminada del Plan inicial. En esta situación, su factura se reducirá en 125 dólares al mes. Puede que se encuentre en este caso al colaborar con otro miembro del entorno del Plan inicial, donde cada uno de ustedes solo necesite un igual único. Su factura se refleja en la Figura 6 si elimina un igual de la red del Plan inicial.

| Componentes de tarifas | Coste al mes |
| ----- | ---------------- |
| Red 1 de la cuota de suscripción | 250 dólares |
| Cuota de iguales | 125 dólares |
| Cuota de iguales | N/D |
| Fin del primer cargo mensual |  375 dólares |

*Figura 6. Cargo de una red predeterminada del Plan inicial después de eliminar un igual*


## Tarifas del Plan empresarial
{: #ibp-pricing-enterprise-plan}

{{site.data.keyword.blockchainfull_notm}} Platform no proporciona configuración predeterminada para una red del Plan empresarial. Puede elegir la configuración con la que desee empezar. Cuando esté listo para utilizar el Plan empresarial, debería comprender bien cómo debería ser su configuración de red. Como mejor práctica de alta disponibilidad, recomendamos encarecidamente un mínimo de dos iguales por organización para garantizar que la organización no experimente un corte de red.

Si está en una red de Plan empresarial con el otro miembro de la red, y cada uno de ustedes añade dos iguales para la organización, cada una de sus facturas se reflejará en la Figura 7.

| Componentes de tarifas | Coste al mes |
|-----|----------------|
| Cuota de suscripción | 1000 dólares |
| Cuota de iguales | 1000 dólares |
| Cuota de iguales | 1000 dólares |
| Fin del cargo mensual | 3000 dólares |

*Figura 7. Cargo de una red básica del Plan empresarial*

### Tarifas de ejemplo del Plan empresarial
{: #ibp-pricing-enterprise-plan-pricing}


#### Iguales adicionales
{: #ibp-pricing-enterprise-additional-peers}

Continúe con el ejemplo anterior, si añade otros dos iguales a la organización. Su factura aumenta en 2000 dólares al mes. Su factura se refleja en la Figura 8:

| Componentes de tarifas | Coste al mes |
|-----|----------------|
| Cuota de suscripción | 1000 dólares |
| Cuota de iguales | 1000 dólares |
| Cuota de iguales | 1000 dólares |
| Cuota de iguales | 1000 dólares |
| Cuota de iguales | 1000 dólares |
| Fin del primer cargo mensual | 5000 dólares |

*Figura 8. Cargo de una red del Plan empresarial con cuatro iguales*

Si el otro miembro conserva la misma configuración de red, la factura del miembro tendrá el mismo aspecto que antes, lo que se refleja en la Figura 7.

#### Redes adicionales
{: #ibp-pricing-enterprise-additional-networks}

El Plan empresarial tampoco restringe el número a instancias de redes a las que puede suministrar o unirse. Si crea una segunda red del Plan empresarial con la misma configuración de red básica, es decir, una única organización con dos iguales, la factura se refleja en la Figura 11. Puede que se encuentre en este caso al crear varias redes, al unirse a varias redes, o una combinación de los dos.

| Componentes de tarifas | Coste al mes |
|-----|----------------|
| Red 1 de la cuota de suscripción | 1000 dólares |
| Cuota de iguales | 1000 dólares |
| Cuota de iguales | 1000 dólares|
| Red 2 de la cuota de suscripción | 1000 dólares |
| Cuota de iguales | 1000 dólares |
| Cuota de iguales | 1000 dólares|
| Total | 6000 dólares |

*Figura 9. Cargo de dos redes del Plan empresarial con la configuración de red básica*

Si el otro miembro solo utiliza una red de Plan empresarial y conserva la misma configuración de red, la factura del miembro tendrá el mismo aspecto que antes, lo que se refleja en la Figura 7.
