---

copyright:
  years: 2017, 2018
lastupdated: "2018-12-07"
---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:codeblock: .codeblock}
{:screen: .screen}
{:pre: .pre}

# Cómo escribir contratos inteligentes

***[¿Le resulta útil esta página? Indíquenos su opinión.](https://www.surveygizmo.com/s3/4501493/IBM-Blockchain-Documentation)***

El código de encadenamiento, al que también se hace referencia como contratos inteligentes, es software que le permite leer y actualizar datos en el libro mayor de blockchain. El código de encadenamiento puede convertir la lógica empresarial en un programa ejecutable acordado y verificado por todos los miembros de la red blockchain. La lógica empresarial incluye la definición de activos negociados entre las partes. También incluye los términos y condiciones necesarios para que se ejecute una transacción. La transformación de estas reglas en código en blockchain permite que las empresas puedan optimizar los procesos empresariales y auditorías, así como reducir en gran medida el procesamiento manual y el papeleo.

Como ejemplo, imagine que una red de concesionarios de automóviles, compañías de seguros y reguladores gubernamentales ha decidido utilizar blockchain para realizar un seguimiento de la propiedad de los vehículos. El código de encadenamiento podría requerir que todos los vehículos tengan un registro y un número de identificación de vehículo válidos para que se puedan añadir a la red. Cuando se vende un vehículo, es posible que el código de encadenamiento requiera que se pongan fondos en depósito hasta que se registre el vehículo para un nuevo propietario a través de un regulador. Cuando finalice el registro, el nuevo propietario se registrará y se transferirán los fondos automáticamente.

La guía de aprendizaje siguiente le mostrará los aspectos básicos de la creación del código de encadenamiento, incluyendo:

- [Cómo empezar a escribir código de encadenamiento](#write-chaincode)
- [La relación entre el código de encadenamiento y los datos](#install-chaincode)
- [Transacciones de código de encadenamiento cruzadas](#cross-chaincode-transactions)

La guía de aprendizaje también presenta aspectos importantes de Fabric que son accesibles a través del código de encadenamiento:

- [Datos privados](#private-data)
- [Utilización de índices con couchDB](#indexes)

## Cómo escribir código de encadenamiento
{: #write-chaincode}

El código de encadenamiento se puede escribir en varios lenguajes, y la plataforma {{site.data.keyword.blockchainfull_notm}} admite código de encadenamiento escrito en Go y en Node.js. El código de encadenamiento permite que los usuarios puedan consultar y cambiar datos almacenados en blockchain utilizando las API que proporciona la interfaz de código de encadenamiento de Fabric. Los datos de blockchain se almacenan en pares clave-valor en el escenario mundial del
[libro mayor
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/ledger/ledger.html "Libro mayor") del canal. El código de encadenamiento utiliza mandatos get para recuperar valores y mandatos put para crear o actualizar valores. Mediante el uso de estas operaciones básicas, puede crear funciones que definen las reglas empresariales de la red. Estas funciones las pueden invocar las aplicaciones y pueden emerger para los usuarios finales de la red. Siguiendo con el ejemplo de la red de vehículos, puede crear una función que permita que un concesionario de automóviles pueda utilizar un mandato put para añadir un coche nuevo al libro mayor únicamente si proporciona un número de ID de vehículo válido.

Puede obtener información sobre cómo empezar a escribir código de encadenamiento consultando la
[Guía de aprendizaje de código de encadenamiento para desarrolladores ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4ade.html "Guía de aprendizaje de código de encadenamiento para desarrolladores") en la documentación de la comunidad de Hyperledger Fabric. La guía de aprendizaje le guiará a través de la construcción de un código de encadenamiento simple que crea y lee activos, y le presentará las API que se utilizan en el proceso. También puede encontrar la guía de referencia de API del código de encadenamiento para todos los lenguajes de código de encadenamiento. Hay ejemplos adicionales en la carpeta de código de encadenamiento del [repositorio de ejemplos de Fabric
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://github.com/hyperledger/fabric-samples "Ejemplos de Fabric").

## Instalación de código de encadenamiento
{: #install-chaincode}

Debido a que el código de encadenamiento proporciona la estructura de transacciones en un canal, es necesario instalar un código de encadenamiento en todos los iguales que se hayan unido al canal que desea utilizar el código de encadenamiento para actualizar o consultar el libro mayor del canal. A continuación, un miembro del canal puede crear una instancia del código de encadenamiento en un canal y establecer la política de aprobación del código de encadenamiento. La instalación y la creación de la instancia del código de encadenamiento se puede realizar utilizando la interfaz de usuario del supervisor de red, la interfaz de línea de mandatos de igual de Fabric o desde la aplicación cliente mediante el
[SDK de Fabric](/docs/services/blockchain/v10_application.html#operate-sdk). Para obtener información sobre cómo utilizar la interfaz de usuario del supervisor de red para desplegar código de encadenamiento, consulte [Instalación, creación de instancias y actualización de un código de encadenamiento](/docs/services/blockchain/howto/install_instantiate_chaincode.html).

## Código de encadenamiento y datos
{: #chaincode-data}

Cada canal tiene solo un libro mayor, y los datos de dicho libro mayor se particionan mediante una clave exclusiva y el código de encadenamiento que añadió el par de clave-valor al libro mayor. Los miembros solo pueden leer o actualizar datos en el libro mayor del canal utilizando la clave correcta y el código de encadenamiento asociado. Los datos a los que puede acceder un código de encadenamiento se conocen como espacio de nombres del código de encadenamiento y todos los datos del libro mayor están dentro del espacio de nombres de un código de encadenamiento. Un código de encadenamiento puede interactuar con los datos fuera de su espacio de nombres únicamente mediante una [transacción de código de encadenamiento cruzada](#cross-chaincode-transactions) con el código de encadenamiento que puede acceder a los datos relevantes.

Podemos utilizar el código de encadenamiento de fabcar del repositorio de ejemplos de Fabric como ejemplo de cómo interactúa el código de encadenamiento con los datos. El espacio de nombres se crea cuando se crea la instancia del contrato inteligente. Algunos códigos de encadenamiento aceptan un conjunto de argumentos de par de clave-valor cuando se crea la instancia del código de encadenamiento, y utilizan dichos datos para inicializar el espacio de nombres. El código de encadenamiento de fabcar del repositorio de ejemplos de Fabric no acepta ningún argumento cuando se crea su instancia. Para fabcar, debe añadir datos al espacio de nombres utilizando la función initLedger o createCar. Por ejemplo, pasando el argumento `{"Args":["createCar",'CAR1', 'Honda', 'Accord', 'Black', 'Tom']}`, se creará el par de clave-valor
`{Key='CAR1', value={'Honda', 'Accord', 'Black', 'Tom'}}` en el espacio de nombres de fabcar.

Únicamente el código de encadenamiento de fabcar puede cambiar estos datos. Supongamos que el usuario desea transferir el coche a un nuevo propietario utilizando la función changeCarOwner que se indica a continuación:
```
func (s *SmartContract) changeCarOwner(APIstub shim.ChaincodeStubInterface, args []string) sc.Response {

	if len(args) != 2 {
		return shim.Error("Incorrect number of arguments. Expecting 2")
	}

	carAsBytes, _ := APIstub.GetState(args[0])
	car := Car{}

	json.Unmarshal(carAsBytes, &car)
	car.Owner = args[1]

	carAsBytes, _ = json.Marshal(car)
	APIstub.PutState(args[0], carAsBytes)

	return shim.Success(nil)
}
```
{:codeblock}

Puede invocar el código de encadenamiento utilizando los argumentos siguientes:
```
{"Args":["changeCarOwner",'CAR1','Mark']}
```
{:codeblock}

A continuación, el código de encadenamiento utiliza los mandatos get para generar un conjunto de lectura del espacio de nombres del código de encadenamiento. Utiliza los mandatos para crear el conjunto de escritura de transacción. La invocación del código de encadenamiento también crea un ID de transacción exclusivo.
```
TxId = Txid1
Read Set: `{Key='CAR1', value[3]='Tom'}`
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

Cada igual de aprobación con el código de encadenamiento instalado simulará todas las transacciones utilizando el conjunto de lectura y el conjunto de escritura. Si las simulaciones de cada igual son coherentes, las transacciones se consideran válidas y se pueden confirmar en el libro mayor. Después de la transacción, el espacio de nombres de fabcar pasará a ser `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Mark'}`. Observe que el propietario ha cambiado de Tom a Mark.

La relación 1-1 entre el código de encadenamiento y los datos crea importantes consideraciones para el desarrollo de redes y aplicaciones. El software de código de encadenamiento se debe actualizar mediante la instalación de un código de encadenamiento con el mismo nombre y una versión distinta, en lugar de utilizar un nuevo código de encadenamiento. Este procedimiento de actualización conserva el espacio de nombres del código de encadenamiento y le permite seguir utilizando los datos existentes. También puede utilizar un código de encadenamiento distinto para restringir el acceso a datos confidenciales, o leer y escribir datos en paralelo.

## Transacciones de código de encadenamiento cruzadas
{: #cross-chaincode-transactions}

Puede utilizar un código de encadenamiento para invocar otros códigos de encadenamiento. Esto permite que un código de encadenamiento pueda consultar y escribir datos fuera de su espacio de nombres. El código de encadenamiento puede tanto leer como actualizar datos utilizando el código de encadenamiento instanciado en el mismo canal. No obstante, solo puede consultar datos utilizando código de encadenamiento en canales distintos. La organización que invoca el código de encadenamiento original debe tener autorización para invocar el código de encadenamiento que es el objetivo de la transacción.

Por ejemplo, puede utilizar la función `crossChaincodeChangeCarOwner` siguiente para llamar a fabcar desde otro código de encadenamiento. Debido a que la función actualiza los datos cambiando el propietario del vehículo, debe llamar al código de encadenamiento de fabcar instanciado en el mismo canal.
```
func (s *SmartContract) crossChaincodeChangeCarOwner(APIstub shim.ChaincodeStubInterface, args []string) sc.Response {
	newOwnerArgs := toChaincodeArgs("changeCarOwner", args[0], args[1])

	chaincodeName := "fabcar"
	channelName := "defaultchannel"

	APIstub.InvokeChaincode(chaincodeName, newOwnerArgs, channelName)

	return shim.Success(nil)
}
```
{:codeblock}

Si crea un nuevo contrato utilizando este código e invoca la función `crossChaincodeChangeCarOwner`, la transacción generará un nuevo ID de transacción y un conjunto de escritura.
```
Contract: newContract
TxId = Txid2
Write Set: `{Key='CAR1', value[3]='Mark'}`
```
{:codeblock}

Este TXid y este conjunto de escritura se pasarán al código de encadenamiento de fabcar.
```
Contract: fabcar
TxId = Txid2
Read Set: `{Key='CAR1', value[3]='Mark'}`
Write Set: `{Key='CAR1', value[3]='Joe'}`
```
{:codeblock}

Debido a que `fabcar` se encuentra en el mismo canal que `newContract`, la función
`crossChaincodeChangeCarOwner` tiene permitido escribir en el espacio de nombres de `fabcar`. El nuevo espacio de nombres de `fabcar` será `{Key='CAR1', value='Honda', 'Accord', 'Black', 'Joe'}`.

## Datos privados
{: #private-data}

Las recopilaciones de datos privados son una característica de las redes de Hyperledger Fabric que tiene la versión 1.2 o superior. Los grupos de organizaciones utilizan canales para realizar transacciones sin exponer los datos a otras organizaciones. No obstante, es posible que las organizaciones deseen mantener la información confidencial como privada, sin exponer los datos a los demás miembros del canal. En este caso, pueden utilizar recopilaciones de datos privados para almacenar información únicamente en los iguales de las organizaciones que necesiten los datos. Por ejemplo, un mayorista y una granja podrían querer utilizar el mismo canal que otras granjas para comprobantes de venta. Sin embargo, podrían utilizar una recopilación privada para mantener privados los aspectos confidenciales de la venta, como el precio. Para obtener más información sobre el uso de datos privados dentro de blockchain, consulte el artículo de concepto
[Datos privados
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/chaincode4ade.html "Datos privados") en la documentación de Fabric.

Puede utilizar datos privados en la plataforma {{site.data.keyword.blockchainfull_notm}} siempre que la red tenga la versión de Fabric 1.2 o superior. Puede añadir un archivo de configuración de recopilación de datos privados al código de encadenamiento antes de instalarlo en el igual. A continuación, puede utilizar API de código de encadenamiento específicas para datos privados y recuperar datos de la recopilación.

Para obtener más información sobre cómo utilizar recopilaciones de datos privados con el código de encadenamiento, consulte
[Utilización de datos privados
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/latest/private_data_tutorial.html "Utilización de datos privados") en la documentación de Fabric. No puede utilizar el supervisor de red para instalar código de encadenamiento con un archivo de configuración de recopilación. No obstante, puede utilizar los SDK de Fabric para instalar, crear una instancia o actualizar un código de encadenamiento que utilice datos privados. Para obtener más información, consulte [Cómo utilizar datos privados
![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://fabric-sdk-node.github.io/release-1.3/tutorial-private-data.html "Cómo utilizar datos privados") en la documentación del SDK de Node. Tenga en cuenta que deberá cargar el signCert del SDK en la plataforma
{{site.data.keyword.blockchainfull_notm}} para poder utilizar los SDK para instalar y crear una instancia del código de encadenamiento en el igual. Para obtener más información sobre cómo interactuar y operar con la red con los SDK de Fabric, consulte
[Desarrollo de aplicaciones](/docs/services/blockchain/v10_application.html) y [Trabajar con la red utilizando el SDK](/docs/services/blockchain/v10_application.html#operate-sdk).

## Utilización de índices con CouchDB
{: #indexes}

Si los datos de libro mayor se almacenan en CouchDB, se recomienda encarecidamente que cree índices para las consultas de CouchDB y los utilice en el código de encadenamiento. Los índices permiten que sus aplicaciones puedan recuperar datos de forma eficiente a medida que la red añade bloques adicionales de transacciones y entradas en el escenario mundial. Además, CouchDB el permite realizar consultas de datos enriquecidas desde su código de encadenamiento sobre un libro mayor de canal.

Para obtener más información sobre CouchDB y cómo configurar índices, consulte
[CouchDB como base de datos de estado ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](http://hyperledger-fabric.readthedocs.io/en/release-1.1/couchdb_as_state_database.html "CouchDB como base de datos de estado"){:new_window} en la documentación de Hyperledger Fabric. También encontrará un ejemplo que utiliza un índice con código de encadenamiento en la [guía de aprendizaje de CouchDB de Fabric ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.2/couchdb_tutorial.html). Consulte
[Prácticas recomendadas al utilizar CouchDB](/docs/services/blockchain/v10_application.html#couchdb-indices) en la guía de aprendizaje de desarrollo de aplicaciones para obtener más información sobre cómo consultar datos desde sus aplicaciones.
