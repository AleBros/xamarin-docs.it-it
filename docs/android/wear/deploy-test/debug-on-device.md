---
title: Eseguire il debug in un dispositivo Wear
description: Questo articolo illustra come eseguire il debug di un'applicazione xamarin. Android Wear in un dispositivo Wear.
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/16/2018
ms.openlocfilehash: 816ec5c861b5889e1735eab6293ed10318c53644
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67831885"
---
# <a name="debug-on-a-wear-device"></a>Eseguire il debug in un dispositivo Wear

_Questo articolo illustra come eseguire il debug di un'applicazione xamarin. Android Wear in un dispositivo Wear._


## <a name="overview"></a>Panoramica

Se si dispone di un dispositivo Android Wear, ad esempio un Smartwatch Android Wear, è possibile eseguire l'app sul dispositivo invece di usare un emulatore. (Se non si ha ancora familiarità con il processo di distribuzione ed esecuzione di App Android Wear, vedere [salve, Wear](~/android/wear/get-started/hello-wear.md).)

## <a name="prepare-the-wear-device"></a>Preparare il dispositivo Wear:

Per abilitare il debug nel dispositivo Android Wear, procedere come segue:

1.  Aprire il **impostazioni** menu nel dispositivo Android Wear.

2.  Scorrere fino alla fine del menu di scelta e toccare **sulle**.

3.  Toccare il numero di build 7 volte.

4.  Nel **le impostazioni** dal menu **opzioni sviluppatore**.

5.  Verificare che **ADB debug** è abilitata.


## <a name="debugging-over-usb"></a>Il debug tramite USB

Se il dispositivo Wear dispone di una porta USB, è possibile connettere il dispositivo Wear al computer, distribuirlo e run/debug dell'app come se si utilizzassero un telefono Android (per altre informazioni, vedere [eseguire il Debug in un dispositivo](~/android/deploy-test/debugging/debug-on-device.md)).


## <a name="debugging-over-bluetooth"></a>Il debug tramite Bluetooth

Se il dispositivo Wear non ha una porta USB, è possibile distribuire l'app al dispositivo Wear tramite Bluetooth dal routing dell'output di debug dell'app a un telefono Android connessa al computer in uso. 

### <a name="prepare-your-phone"></a>Preparare il telefono

Per preparare il telefono per le connessioni Bluetooth nel dispositivo Wear, procedere come segue: 

1.  Se non è già stato fatto, impostare il telefono per lo sviluppo di xamarin. Android come descritto in [configurare il dispositivo per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md).

2.  Scaricare e installare la versione gratuita [Android Wear](https://play.google.com/store/apps/details?id=com.google.android.wearable.app) app dallo Store di Play di Google.

### <a name="connect-the-device"></a>Connettere il dispositivo

Usare la procedura seguente per connettere il dispositivo Wear al telefono dell'utente:

1.  Sul telefono che fungono da intermediario Bluetooth (configurato in precedenza), avviare l'app Android Wear. 

2.  Toccare il **impostazioni** icona.

3.  Abilitare **debug tramite Bluetooth**. Verrà visualizzato il seguente stato visualizzato nella schermata dell'app Android Wear:

        Host: disconnected
        Target: connected

4.  Connettere il telefono al computer tramite USB. Nel computer, immettere i comandi seguenti:

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    Se la porta 4444 non è disponibile, è possibile usare qualsiasi altra porta disponibile a cui si ha accesso. 

    > [!NOTE]
    > Se si riavvia Visual Studio o Visual Studio per Mac, è necessario eseguire questi comandi per configurare una connessione al dispositivo Wear.

5.  Quando il dispositivo Wear richiesto, confermare che consentano **ADB debug**. Nell'app Android Wear, si potrà osservare lo stato di passare a:

        Host: connected
        Target: connected

6.  Dopo aver completato i passaggi precedenti, in esecuzione `adb devices` Mostra lo stato del telefono sia il dispositivo Android Wear:

        List of devices attached
        127.0.0.1:4444    device
        019ad61df0a69399  device

A questo punto, è possibile distribuire l'app al dispositivo Wear.

<a name="screenshots" />

### <a name="taking-screenshots"></a>Screenshot

È possibile acquisire una schermata del dispositivo Wear immettendo il comando seguente: 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

Copia la schermata di computer in uso immettendo il comando seguente:

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

Eliminare la schermata del dispositivo immettendo il comando seguente:

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```


### <a name="uninstalling-an-app"></a>Disinstallazione di un'app

È possibile disinstallare un'app dal dispositivo wear immettendo il comando seguente:

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

Ad esempio, per rimuovere l'app con il nome del pacchetto `com.xamarin.weartest`, immettere il comando seguente:

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

Per altre informazioni sul debug di dispositivi Android Wear tramite Bluetooth, vedere [debug tramite Bluetooth](https://developer.android.com/training/wearables/apps/bt-debugging.html).


## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>Debug di un'app per Wear con un'app per telefono companion

Inclusi nello stesso pacchetto App Android Wear con un'app per telefono Android complementare per la distribuzione in Google Play (per altre informazioni, vedere [utilizzo di creazione di pacchetti](~/android/wear/deploy-test/packaging.md)). Tuttavia, è comunque sviluppare l'app Wear e le app complementare separatamente. Quando si rilascia l'app tramite il Store di Google Play, l'app Wear verrà incluso nel pacchetto con l'app complementare e installato automaticamente se possibile.

Per eseguire il debug dell'app Wear con un'app complementare: 

1.  Compilare e distribuire l'app complementare al telefono.

2.  Fare clic sul progetto Wear e impostarlo come progetto di avvio predefinito.

3.  Distribuire il progetto Wear al dispositivo indossabile.

4.  Eseguire e il debug dell'app Wear nel dispositivo.

 
## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come configurare un dispositivo Android Wear per Wear debug da Visual Studio tramite Bluetooth e come eseguire il debug di un'app per Wear con un'app per telefono complementare. Fornito anche suggerimenti di debug comuni per il debug di un'app Wear tramite Bluetooth.
