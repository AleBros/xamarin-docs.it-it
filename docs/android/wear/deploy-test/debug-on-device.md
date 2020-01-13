---
title: Eseguire il debug in un dispositivo Wear
description: Questo articolo illustra come eseguire il debug di un'applicazione Xamarin.Android Wear in un dispositivo Wear.
ms.prod: xamarin
ms.assetid: 01668E4B-BB83-4C26-B23A-F788173FB823
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 965ed4e802c05f8450192c0fec17fe31e464c779
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73028689"
---
# <a name="debug-on-a-wear-device"></a>Eseguire il debug in un dispositivo Wear

_Questo articolo illustra come eseguire il debug di un'applicazione Xamarin.Android Wear in un dispositivo Wear._

## <a name="overview"></a>Panoramica

Se si dispone di un dispositivo Android Wear, ad esempio un SmartWatch Android Wear, è possibile eseguire l'app sul dispositivo anziché usare un emulatore. Se non si ha ancora familiarità con il processo di distribuzione ed esecuzione di app Android Wear, vedere [Hello, Wear](~/android/wear/get-started/hello-wear.md).

## <a name="prepare-the-wear-device"></a>Preparare il dispositivo Wear:

Per abilitare il debug nel dispositivo Android Wear, attenersi alla procedura seguente:

1. Aprire il menu **Impostazioni** nel dispositivo Android Wear.

2. Scorrere fino alla fine del menu e toccare **About (informazioni**).

3. Toccare il numero di Build 7 volte.

4. Scegliere Opzioni per gli **sviluppatori**dal menu **Impostazioni** .

5. Confermare che il **debug ADB** è abilitato.

## <a name="debugging-over-usb"></a>Debug su USB

Se il dispositivo Wear ha una porta USB, è possibile connettere il dispositivo Wear al computer, distribuirlo ed eseguire/eseguire il debug dell'app come si usa un telefono Android (per altre informazioni, vedere [debug in un dispositivo](~/android/deploy-test/debugging/debug-on-device.md)).

## <a name="debugging-over-bluetooth"></a>Debug su Bluetooth

Se il dispositivo Wear non dispone di una porta USB, è possibile distribuire l'app nel dispositivo Wear tramite Bluetooth indirizzando l'output di debug dell'app a un telefono Android connesso al computer. 

### <a name="prepare-your-phone"></a>Preparare il telefono

Usare la procedura seguente per preparare il telefono per l'esecuzione di connessioni Bluetooth al dispositivo Wear: 

1. Se non è già stato fatto, configurare il telefono per lo sviluppo di Xamarin.Android, come illustrato in [configurare il dispositivo per lo sviluppo](~/android/get-started/installation/set-up-device-for-development.md).

2. Scaricare e installare l'app [Android Wear](https://play.google.com/store/apps/details?id=com.google.android.wearable.app) gratuita dalla Google Play Store.

### <a name="connect-the-device"></a>Connettere il dispositivo

Usare la procedura seguente per connettere il dispositivo Wear al telefono:

1. Sul telefono che fungerà da intermediario Bluetooth (configurato in precedenza), avviare l'app Android Wear. 

2. Toccare l'icona **delle impostazioni** .

3. Abilitare il **debug su Bluetooth**. Sullo schermo dell'app Android Wear dovrebbe essere visualizzato lo stato seguente:

    ```
    Host: disconnected
    Target: connected
    ```

4. Connettere il telefono al computer tramite USB. Nel computer immettere i comandi seguenti:

    ```shell
    adb forward tcp:4444 localabstract:/adb-hub
    adb connect 127.0.0.1:4444
    ```

    Se la porta 4444 non è disponibile, è possibile usare qualsiasi altra porta disponibile a cui si ha accesso. 

    > [!NOTE]
    > Se si riavvia Visual Studio o Visual Studio per Mac, è necessario eseguire di nuovo questi comandi per configurare una connessione al dispositivo Wear.

5. Quando il dispositivo Wear richiede l'utente, verificare che il **debug ADB**venga consentito. Nell'app Android Wear dovrebbe essere visualizzata la modifica dello stato:

    ```
    Host: connected
    Target: connected
    ```

6. Dopo aver completato i passaggi precedenti, l'esecuzione di `adb devices` Mostra lo stato del dispositivo Phone e Android Wear:

    ```
    List of devices attached
    127.0.0.1:4444    device
    019ad61df0a69399  device
    ```

A questo punto, è possibile distribuire l'app nel dispositivo Wear.

<a name="screenshots" />

### <a name="taking-screenshots"></a>Acquisizione di schermate

È possibile scattare una schermata del dispositivo Wear immettendo il comando seguente: 

```shell
adb -s 127.0.0.1:4444 shell screencap -p /sdcard/DCIM/screencap.png
```

Copiare lo screenshot nel computer immettendo il comando seguente:

```shell
adb -s 127.0.0.1:4444 pull /sdcard/DCIM/screencap.png
```

Eliminare lo screenshot del dispositivo immettendo il comando seguente:

```shell
adb -s 127.0.0.1:4444 shell rm /sdcard/DCIM/screencap.png
```

### <a name="uninstalling-an-app"></a>Disinstallazione di un'app

È possibile disinstallare un'app dal dispositivo Wear immettendo il comando seguente:

```shell
adb -s 127.0.0.1:4444 uninstall <package name>
```

Ad esempio, per rimuovere l'app con il nome del pacchetto `com.xamarin.weartest`, immettere il comando seguente:

```shell
adb -s 127.0.0.1:4444 uninstall com.xamarin.weartest
```

Per altre informazioni sul debug di dispositivi Android Wear tramite Bluetooth, vedere [debug su Bluetooth](https://developer.android.com/training/wearables/apps/bt-debugging.html).

## <a name="debugging-a-wear-app-with-a-companion-phone-app"></a>Debug di un'app Wear con un'app per telefono complementare

Le app Android Wear sono assemblate con un'app per telefoni Android complementare per la distribuzione nel Google Play (per altre informazioni, vedere uso della creazione di [pacchetti](~/android/wear/deploy-test/packaging.md)). Tuttavia, è comunque possibile sviluppare l'app Wear e la relativa app complementare separatamente. Quando si rilascia l'app tramite il Google Play Store, l'app Wear verrà assemblata con l'app complementare e installata automaticamente, se possibile.

Per eseguire il debug dell'app Wear con un'app complementare: 

1. Compilare e distribuire l'app complementare sul telefono.

2. Fare clic con il pulsante destro del mouse sul progetto Wear e impostarlo come progetto di avvio predefinito.

3. Distribuire il progetto Wear sul dispositivo indossabile.

4. Eseguire ed eseguire il debug dell'app Wear sul dispositivo.

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato come configurare un dispositivo Android Wear per l'uso del debug da Visual Studio tramite Bluetooth e come eseguire il debug di un'app Wear con un'app per telefono complementare. Sono stati inoltre forniti suggerimenti comuni per il debug di un'app Wear tramite Bluetooth.
