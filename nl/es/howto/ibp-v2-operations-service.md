---

copyright:
  years: 2019
lastupdated: "2019-05-16"

keywords: logging levels, metrics, health check, peer, orderer

subcollection: blockchain

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen: .screen}
{:codeblock: .codeblock}
{:note: .note}
{:important: .important}
{:tip: .tip}
{:pre: .pre}

# Funcionamiento de nodos con el servicio de operaciones
{: #operations_service}

{{site.data.keyword.blockchainfull}} Platform v2.0 se basa en Hyperledger Fabric v1.4.1. La plataforma admite la característica de servicio de operaciones, que ofrece una API de "operaciones" RESTful para que los operadores puedan realizar comprobaciones de estado de nodos, extraer métricas operativas de los nodos de igual y clasificador y gestionar los niveles de registro. El igual y el clasificador alojan un servidor HTTP que ofrece la API de "operaciones" RESTful.  Para obtener más información sobre el servicio de operaciones, consulte [El servicio de operaciones ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html "El servicio de operaciones"){:new_window}.
{:shortdesc}


## Consideraciones y limitaciones
{: #operations_service_consideration_limitation}

- Todos los nodos de igual y clasificador se configuran con `clientAuthRequired: false`, de manera que se pueda acceder al comprobador de estado. Debido a que `clientAuthRequired` se establece en `false` y que también está habilitado el TLS mutuo, al acceder al servidor REST, necesitará pasar las identidades TLS para poder autenticarse. Este valor garantiza que únicamente los usuarios que tengan las claves adecuadas podrán ver los registros correspondientes.
- Por ahora, solo hay soporte para el soporte de extracción de métricas basado en [Prometheus ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#prometheus).

## Antes de empezar
{: #operations_service_before_you_begin}

Necesita recopilar la información siguiente del igual y el clasificador.

- **`peer-endpoint`** u **`orderer-endpoint`**

  Puede encontrar el URL de punto final de operaciones del igual o el clasificador en el archivo JSON del igual o el clasificador que exporte desde la consola.

    1. Pulse sobre el igual o el clasificador en el separador **Nodo**.
    2. En la página del igual o el clasificador, pulse el icono **Valores** situado junto al nombre del igual o el clasificador.
    3. En el panel lateral, pulse **Exportar** para guardar el archivo JSON del igual o el clasificador.
    4. Busque el URL del punto final de operaciones, que es el valor del parámetro `operations_url` en el archivo JSON. Se hace referencia a este valor como `peer-endpoint` u `orderer-endpoint` en los mandatos posteriores de este tema. Por ejemplo:

      ```
      {
      "short_name": "Peer1Org1_0",
      "name": "Peer1 Org1",
      "url": "https://169.46.208.93:32739",
      "type": "fabric-peer",
      "msp_id": "org1msp",
      "operations_url": "https://169.46.208.93:32101"
      }
      ```

      ```
      {
      "short_name": "Orderer_0",
      "name": "Orderer",
      "url": "https://169.46.208.93:31612",
      "type": "fabric-orderer",
      "msp_id": "orderermsp",
      "operations_url": "https://169.46.208.93:30115"
      }
      ```

- **`client-tls-cert`** y **`client-tls-key`**

  Puede encontrar el certificado y la clave privada de la CA TLS en la consola.

  1. Pulse sobre el nodo de CA del igual o el clasificador en el separador **Nodo**.
  2. En la página CA, pulse **Entidad emisora de certificados TLS**.
  3. En la tabla CA TLS, localice el usuario `admin` y pulse sobre los puntos verticales en **Acciones**. A continuación, pulse **Inscribir identidad**.
  4. En el panel lateral, pulse **Siguiente** y podrá ver el certificado y la clave privada de la CA TLS.
  5. Proporcione un nombre de visualización para la identidad y pulse **Exportar identidad** y, a continuación, **Añadir identidad a la cartera** para guardar el certificado y la clave privada de la CA TLS en un archivo JSON.
  6. Abra el archivo JSON exportado.
  7. Busque la clave privada, que es el valor del parámetro `private_key` en el archivo JSON. Esta es la `client-tls-key` para su uso en los mandatos posteriores.
  8. Busque el certificado de CA TLS, que es el valor del parámetro `cert` en el archivo JSON. Este es el `client-tls-cert` que se utilizará en los mandatos posteriores.

- **`peer tls-ca cert`** u **`orderer tls-ca cert`**

  Puede encontrar el certificado de CA TLS del igual o el clasificador en la consola.

  1. Pulse sobre el nodo de igual o clasificador en el separador **Nodo**.
  2. Pulse el icono **Valores** situado junto al nombre de nodo.
  3. Copie la serie de certificado de CA TLS del nodo. Este es el `peer tls-ca cert` u `orderer tls-ca cert` que se puede utilizar en los mandatos siguientes.

**Nota:** para todos los certificados y claves, debe convertir las series de certificado, que están en formato `base64`, en archivos `.pem` individuales utilizando los mandatos siguientes:
  ```
  export FLAG=$(if [ "$(uname -s)" == "Linux" ]; then echo "-w 0"; else echo "-b 0"; fi)
  echo <base64_string> | base64 --decode $FLAG > <key_name>.pem
  ```
  {:codeblock}
{:important}


## Comprobación del estado del nodo
{: #operations_service_health_check}

Ejecute el mandato `curl -k <peer-endpoint>/healthz` o `curl -k <orderer-endpoint>/healthz` para comprobar el estado del nodo igual o clasificador. Por ejemplo:

```
curl -k https://169.46.208.93:3210/healthz
```
{:codeblock}

Para obtener más información sobre las comprobaciones de estado, consulte [Comprobaciones de estado ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#health-checks "Comprobaciones de estado").


## Visualización de las métricas
{: #operations_service_view_metrics}

Ejecute el mandato siguiente para ver las métricas de igual. Para ver las métricas de clasificador, debe ejecutar el mismo mandato pero sustituyendo `<peer-endpoint>` por `<orderer-endpoint>`.

```
curl -k <peer-endpoint>/metrics --cert <client-tls-cert> --key <client-tls-key> --cacert <peer tls ca-cert>
```
{:codeblock}

Por ejemplo:

```
curl -k https://169.55.231.152:30766/metrics --cert msp/org1/ca/tls/msp/signcerts/cert.pem --key msp/org1/ca/tls/msp/key.pem  --cacert msp/org1/ca/tls/msp/cacerts/tlsca.pem
```
{:codeblock}


Para obtener más información sobre las métricas, consulte [Métricas ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#metrics "Métricas").


## Visualización de niveles de registro
{: #operations_service_log_level_view}

Ejecute el mandato siguiente para ver el nivel de registro. Verá el nivel de registro en el terminal después de que finalice el mandato.

```
curl -k <peer-endpoint>/logspec --cert <client-tls-cert> --key <client-tls-key> --cacert <peer tls ca-cert>
```
{:codeblock}

Por ejemplo:
```
curl -k https://169.46.208.93:3210/logspec --cert msp/org1/ca/tls/msp/signcerts/cert.pem --key msp/org1/ca/tls/msp/keystore/key.pem  --cacert msp/org1/ca/tls/msp/cacerts/tlsca.pem
```

<!--
```
curl https://169.46.208.93:3210/logspec --cert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/signcerts/cert.pem --key temp/1mycluster-test-32240/msp/org1/ca/tls/msp/keystore/3fb20abb935f88b83a8da68317a44a4fa0953d7ec6d06bb19a6fc3979a603095_sk  --cacert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/cacerts/169-55-231-152-30021-tlsca.pem
```
-->

Podrá ver un resultado similar al ejemplo siguiente:

```
{"spec":"info"}
```

## Establecimiento de niveles de registro
{: #operations_service_log_level_set}

Para cambiar el valor de nivel de registro existente, ejecute el mandato siguiente, que utiliza el método `PUT` con cuerpo JSON compuesto por un solo atributo denominado `spec`. Sustituya `<log-level>` por sus niveles de registro esperados. Para obtener más información sobre los niveles de registro que puede establecer, consulte [Especificación del registro ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/logging-control.html#logging-specification "Especificación del registro") en la documentación de Hyperledger Fabric.

```
curl -X PUT  <peer-endpoint>/logspec -d '{"spec":"<log-level>"}' --cert <client-tls-cert> --key <client-tls-key> --cacert <peer tls ca-cert>
```
{:codeblock}

Por ejemplo:
```
curl -X PUT  https://169.46.208.93:3210/logspec -d '{"spec":"chaincode=debug:info"}' --cert msp/org1/ca/tls/msp/signcerts/cert.pem --key msp/org1/ca/tls/msp/keystore/key.pem  --cacert msp/org1/ca/tls/msp/cacerts/tlsca.pem
```

Tras establecer un nuevo nivel de registro, puede utilizar el [mandato view logging level](#operations_service_log_level_view) para comprobar los valores.

<!--
```
curl -X PUT  https://169.46.208.93:3210/logspec -d '{"spec":"chaincode=debug:info"}' --cert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/signcerts/cert.pem --key temp/1mycluster-test-32240/msp/org1/ca/tls/msp/keystore/3fb20abb935f88b83a8da68317a44a4fa0953d7ec6d06bb19a6fc3979a603095_sk  --cacert temp/1mycluster-test-32240/msp/org1/ca/tls/msp/cacerts/169-55-231-152-30021-tlsca.pem
```
-->

Para obtener más información sobre la configuración del nivel de registro, consulte [Gestión del nivel de registro ![Icono de enlace externo](../images/external_link.svg "Icono de enlace externo")](https://hyperledger-fabric.readthedocs.io/en/release-1.4/operations_service.html#log-level-management "Gestión del nivel de registro") en la documentación de Hyperledger Fabric.
