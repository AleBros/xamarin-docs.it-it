---
title: Eseguire il debug su un dispositivo usura
description: In questo articolo viene illustrato come eseguire il debug di un'applicazione usura xamarin su un dispositivo di scadenza.
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.date: 02/16/2018
ms.openlocfilehash: 3f3143dcda4017bbabfbd34a58a40665beea6f75
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="debug-on-a-wear-device"></a>Eseguire il debug su un dispositivo usura

_In questo articolo viene illustrato come eseguire il debug di un'applicazione usura xamarin su un dispositivo di scadenza._


## <a name="overview"></a>Panoramica

Se si dispone di un dispositivo Android, usare, ad esempio un Smartwatch accenti Android, è possibile eseguire l'app sul dispositivo anziché utilizzare un emulatore. (Se non sono ancora familiarità con il processo di distribuzione ed esecuzione di App, usare Android, vedere [salve, usura](~/android/wear/get-started/hello-wear.md).)

## <a name="prepare-the-wear-device"></a>Preparare il dispositivo usura:

Utilizzare la procedura seguente per abilitare il debug nel dispositivo Android, usare:

1.  Aprire il **impostazioni** menu nel dispositivo Android con accenti.

2.  Scorrere fino alla fine del menu di scelta **su**.

3.  Toccare il numero di build 7 volte.

4.  Nel **impostazioni** dal menu **opzioni per gli sviluppatori**.

5.  Verificare che **ADB debug** è abilitata.


## <a name="debugging-over-usb"></a>Il debug tramite USB

Se il dispositivo usura dispone di una porta USB, è possibile connettere il dispositivo usura al computer, distribuire ad esso e di esecuzione/debug dell'app come se si utilizzassero un telefono Android (per ulteriori informazioni, vedere [eseguire il Debug su un dispositivo](~/android/deploy-test/debugging/debug-on-device.md)).


## <a name="debugging-over-bluetooth"></a>Il debug tramite Bluetooth

Se il dispositivo usura non dispone di una porta USB, è possibile distribuire l'app al dispositivo usura tramite Bluetooth dal routing dell'output di debug dell'app per un dispositivo Android che è connesso al computer. 

### <a name="prepare-your-phone"></a>Preparare il telefono

Utilizzare la procedura seguente per preparare il telefono per la creazione di connessioni Bluetooth nel dispositivo usura: 

1.  Se non è già stato fatto, impostare il telefono per lo sviluppo di xamarin come spiegato in [impostare backup dispositivo per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md).

2.  Scaricare e installare gratuitamente [accenti Android](https://play.google.com/store/apps/details?id=com.google.android.wearable.app) app da Google Play Store.

### <a name="connect-the-device"></a>Connettere il dispositivo

Per connettere il dispositivo usura al telefono, procedere come segue:

1.  Sul telefono che verrà fungono da intermediario Bluetooth (configurate in precedenza), avviare l'app Android con accenti. 

2.  Toccare il **impostazioni** icona.

3.  Abilitare **il debug tramite Bluetooth**. Verrà visualizzato il seguente stato visualizzato sullo schermo dell'app Android, usare:

        Host: disconnected
        Target: connected

4.  Telefono di connettersi al computer tramite USB. Nel computer, immettere i comandi seguenti:

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    Se la porta 4444 non è disponibile, è possibile utilizzare qualsiasi altra porta disponibile a cui si ha accesso. 

    **Nota**: se si riavvia Visual Studio o Visual Studio per Mac, è necessario eseguire questi comandi per impostare una connessione al dispositivo usura.

5.  Quando il dispositivo usura viene richiesto, confermare che si consente a **ADB debug**. Nell'app, usare Android, verrà visualizzato lo stato di modificare in:

        Host: connected
        Target: connected

6.  Dopo aver completato i passaggi precedenti, in esecuzione `adb devices` Mostra lo stato del telefono e il dispositivo Android, usare:

        List of devices attached
        127.0.0.1:4444    device
        019ad61df0a69399  device

A questo punto, è possibile distribuire l'app al dispositivo usura.

<a name="screenshots" />

### <a name="taking-screenshots"></a>Screenshot

È possibile acquisire una schermata del dispositivo usura immettendo il comando seguente: 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

Copiare la schermata di computer immettendo il comando seguente:

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

Eliminare la schermata del dispositivo immettendo il comando seguente:

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```


### <a name="uninstalling-an-app"></a>Disinstallare un'applicazione

È possibile disinstallare un'app dal dispositivo usura immettendo il comando seguente:

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

Ad esempio, per rimuovere l'app con il nome del pacchetto `com.xamarin.weartest`, immettere il comando seguente:

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

Per ulteriori informazioni sul debug di Android, usare i dispositivi tramite Bluetooth, vedere [il debug tramite Bluetooth](https://developer.android.com/training/wearables/apps/bt-debugging.html).


## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>Debug di un'app usura con un'app phone complementare

Sono inclusi nel pacchetto App per Android usura con un'app telefono Android complementare per la distribuzione su Google Play (per ulteriori informazioni, vedere [utilizzo di creazione di pacchetti](~/android/wear/deploy-test/packaging.md)). È tuttavia ancora sviluppare l'app usura e app complementare separatamente. Quando si rilascia l'app tramite Google Play Store, app usura verrà incluso nel pacchetto con l'app complementare e installati automaticamente se possibile.

Per eseguire il debug dell'app usura con un'app complementare: 

1.  Compilare e distribuire l'app complementare al telefono.

2.  Fare clic sul progetto usura e impostarlo come progetto di avvio predefinito.

3.  Distribuire il progetto usura al dispositivo indossabile.

4.  Esecuzione e il debug dell'app usura sul dispositivo.

 
## <a name="summary"></a>Riepilogo

Questo articolo ha descritto come configurare un dispositivo Android, usare per il debug di usura da Visual Studio tramite Bluetooth e come eseguire il debug di un'app usura con un'app phone complementare. Inoltre, fornisce suggerimenti di debug comuni per il debug di un'applicazione usura tramite Bluetooth.
