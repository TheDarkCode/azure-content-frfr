<properties
	pageTitle="Simulation d’un appareil avec le Kit de développement logiciel (SDK) de passerelle | Microsoft Azure"
	description="Procédure pas à pas du Kit de développement logiciel (SDK) de passerelle Azure IoT Hub sous Linux pour illustrer l’envoi de données de télémétrie à partir d’un appareil simulé à l’aide du Kit de développement logiciel (SDK) de passerelle Azure IoT Hub."
	services="iot-hub"
	documentationCenter=""
	authors="chipalost"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="04/20/2016"
     ms.author="cstreet"/>


# Kit de développement logiciel (SDK) de passerelle IoT (version bêta) : envoyer des messages appareil-à-cloud avec un appareil simulé à l’aide de Linux

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## Créer et exécuter l’exemple.

Avant de commencer, vous devez :

- [Configurer votre environnement de développement][lnk-setupdevbox] pour utiliser le Kit de développement logiciel (SDK) sous Linux.
- [Créer un hub IoT][lnk-create-hub] dans votre abonnement Azure (vous aurez besoin du nom de votre hub pour effectuer cette procédure pas à pas). Si vous ne possédez pas déjà d’abonnement Azure, vous pouvez obtenir un [compte gratuit][lnk-free-trial].
- Ajoutez deux appareils à votre hub IoT et notez leur ID et leurs clés d'appareil. Vous pouvez utiliser l’outil [Explorateur d’appareils ou iothub-explorer][lnk-explorer-tools] pour ajouter vos appareils au hub IoT que vous avez créé à l'étape précédente et récupérer ainsi leurs clés.

Pour créer l'exemple :

1. Ouvrez un interpréteur de commandes.
2. Accédez au dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk**.
3. Exécutez le script **tools/build.sh**. Ce script utilise l’utilitaire **cmake** pour créer un dossier appelé **build** dans le dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk** et pour générer un makefile. Le script crée ensuite la solution puis exécute les tests.

> [AZURE.NOTE]  Chaque fois que vous exécutez le script **build.sh**, celui-ci supprime et recrée le dossier **build** dans le dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk**.

Pour exécuter l'exemple :

Dans un éditeur de texte, ouvrez le fichier **samples/simulated\_device\_cloud\_upload/src/simulated\_device\_cloud\_upload\_lin.json** dans votre copie locale du référentiel **azure-iot-gateway-sdk**. Ce fichier configure les modules dans l'exemple de passerelle :

- Le module **IoTHub** se connecte à votre hub IoT. Vous devez le configurer pour envoyer des données à votre hub IoT. Plus précisément, définissez la valeur **IoTHubName** sur le nom de votre hib IoT et la valeur **IoTHubSuffix** sur **azure-devices.net**.
- Le module **mapping** mappe les adresses MAC des appareils simulés aux ID de vos appareils IoT Hub. Assurez-vous que les valeurs **deviceId** correspondent aux ID des deux appareils que vous avez ajoutés à votre hub IoT et que les valeurs **deviceKey** contiennent les clés de vos deux appareils.
- Les modules **BLE1** et **BLE2** représentent les appareils simulés. Notez la façon dont leurs adresses MAC correspondent à celles du module **mapping**.
- Le module **Logger** consigne l'activité de votre passerelle dans un fichier.
- Les valeurs **module path** affichées ci-dessous supposent que vous exécuterez l'exemple à partir de la racine de votre copie locale du référentiel **azure-iot-gateway-sdk**.

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "./build/modules/iothubhttp/libiothubhttp_hl.so",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "./build/modules/identitymap/libidentitymap_hl.so",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "GW-ble1-demo",
                    "deviceKey"  : "{Device key}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "GW-ble2-demo",
                    "deviceKey"  : "{Device key}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "./build/modules/logger/liblogger_hl.so",
            "args":
            {
                "filename":"./deviceCloudUploadGatewaylog.log"
            }
        }
    ]
}

```

Enregistrez les modifications apportées au fichier de configuration.

Pour exécuter l'exemple :

1. Dans votre interpréteur de commandes, accédez au dossier racine de votre copie locale du référentiel **azure-iot-gateway-sdk**.
2. Exécutez la commande suivante :

    ```
    ./build/samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ./samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```

3. Vous pouvez utiliser l’outil [Explorateur d’appareils ou iothub-explorer][lnk-explorer-tools] pour analyser les messages qu’IoT Hub reçoit de la passerelle.

## Étapes suivantes

Si vous souhaitez approfondir vos connaissances sur les kits de développement logiciel (SDK) Gateway et découvrir certains exemples de code, consultez les didacticiels de développement et les ressources suivants :

- [Envoi de messages appareil-à-cloud depuis un appareil réel avec le kit de développement logiciel (SDK) Gateway][lnk-physical-device]
- [Gérer un appareil de passerelle][lnk-manage-devices]
- [Kit de développement logiciel (SDK) de la passerelle Azure IoT][lnk-gateway-sdk]

Pour explorer davantage les capacités de IoT Hub, consultez :

- [Conception de votre solution][lnk-design]
- [Guide du développeur][lnk-devguide]
- [Exploration de la gestion des appareils à l’aide de l’exemple d’interface utilisateur][lnk-dmui]
- [Utilisation du portail Azure pour gérer IoT Hub][lnk-portal]
- [Sécuriser votre solution IoT de bout en bout][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-create-hub]: iot-hub-manage-through-portal.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md
[lnk-manage-devices]: iot-hub-gateway-sdk-device-management.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-portal]: iot-hub-manage-through-portal.md
[lnk-securing]: iot-hub-security-ground-up.md

<!---HONumber=AcomDC_0727_2016-->