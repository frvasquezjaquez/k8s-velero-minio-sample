# Kubernetes Backups

En este repositorio estamos instalando velere, utilizando como almacenamiento un bucket en minio. A manera de prueba, este repo, crea un un backup de todos los yamls de kubernetes cada 5 minutos. 

## Instalacion de Minio en Windows
1. Descargamos el ejecutable de minio.
   ```
   https://dl.min.io/server/minio/release/windows-amd64/minio.exe
   ```
2. Inicializamos el servicio
   ```
   .\minio.exe server  C:\Users\fvasquez\Documents\minio-drive --console-address '0.0.0.0:8080' --address '0.0.0.0:9000'
   ```
3. Accedemos a la consola de administracion, con la siguiente url `http://my-minio-server-ip-or-hostname:8080`. Las credenciales por defecto son `minioadmin/minioadmin`

## Inicializacion del Butcket

1. En el panel de administracion de minio, seleccionamos la opcion `Crear Butcket`.
2. Indicamos `kubernetes-backup` como el nombre del bucket.
3. En el panel de administracion de minio, seleccionamos la opcion `Create Service Account`.
4. Debemos asgurarnos de guardar el `Access Key` y el `Secret Key`

## Instalacion de Velero

1. Modificamos el `values.yaml` indicando los siguientes parametros.
   ```
   MY_MINIO_SERVER_IP_OR_DOMAIN: IP o Dominio de nuestro servidor de minio. 
   MINIO_SERVICE_ACCOUNT_KEY_ID: Valor del Access Key del Service Account proveido por minio.
   MINIO_SERVICE_ACCOUNT_ACCESS_KEY: Valor del Secret Key del Service Account proveido por minio.
   ```
2. Instalamos velero utilizando el `values.yaml` que se encuentra en este repo, usando como referencia la [Documentacion de Velero](https://github.com/vmware-tanzu/helm-charts/tree/main/charts/velero)

    ```
    helm repo add vmware-tanzu https://vmware-tanzu.github.io/helm-charts
    helm install vmware-tanzu/velero --namespace velero -f values.yaml velero
    ```

# Verificacion

Si quieremos verificar que los backups esten funcionando correctamente. Debemos ejecutar los siguientes pasos:

1. Instalamos el cliente de velero segun la [Documentacion](https://velero.io/docs/v1.7/basic-install/)
2. Ejecutamos el siguiente comando:
    ```
    velero backup get 

    Output:

    NAME                                                   STATUS      ERRORS   WARNINGS   CREATED                         EXPIRES   STORAGE LOCATION    SELECTOR
    velero-cluster-manifests-full-backups-20211008190022   Completed   0        0          2021-10-08 12:00:22 -0700 PDT   6d        poc-server-backup   <none>

    ```
3. El Status debe ser `Completed`