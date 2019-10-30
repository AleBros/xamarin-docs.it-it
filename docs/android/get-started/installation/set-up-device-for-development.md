---
title: Set Up Device for Development (Configurare il dispositivo per lo sviluppo)
description: Questo articolo illustra come installare un dispositivo Android fisico e connetterlo a un computer in modo che il dispositivo possa essere usato per eseguire applicazioni Xamarin.Android ed eseguirne il debug.
ms.prod: xamarin
ms.assetid: 9116A3AA-EA00-56AF-AE70-BAEEC045EF11
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: f5b92bc202cbf9765a63219ad653442152c3eb24
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020707"
---
# <a name="set-up-device-for-development"></a>Set Up Device for Development (Configurare il dispositivo per lo sviluppo)

_Questo articolo illustra come installare un dispositivo Android e connetterlo a un computer in modo che il dispositivo possa essere usato per eseguire applicazioni Xamarin.Android ed eseguirne il debug._

Al termine dei test in un emulatore Android, sarà utile visualizzare e testare le app in esecuzione in un dispositivo Android. Ecco i passaggi richiesti per la connessione di un dispositivo a un computer per il debug:

1. **Abilitare il debug nel dispositivo** - Per impostazione predefinita, non sarà possibile eseguire il debug di applicazioni in un dispositivo Android.

2. **Installare i driver USB** - Questo passaggio non è necessario per i computer macOS. Per computer Windows può essere richiesta l'installazione di driver USB.

3. **Connettere il dispositivo al computer** - Il passaggio finale prevede la connessione del dispositivo al computer tramite USB o Wi-Fi.

Ognuno di questi passaggi verrà descritto più dettagliatamente nelle sezioni seguenti.

## <a name="enable-debugging-on-the-device"></a>Abilitare il debug nel dispositivo

È possibile usare qualsiasi dispositivo Android per testare un'applicazione Android. Tuttavia, il dispositivo deve essere configurato correttamente prima di poter eseguire il debug. La procedura è leggermente diversa a seconda della versione di Android in esecuzione nel dispositivo.

### <a name="android-40-to-android-41"></a>Da Android 4.0 a Android 4.1

Per le versioni da Android 4.0.x ad Android 4.1.x, è possibile abilitare il debug seguendo questa procedura:

1. Passare alla schermata **Impostazioni**.
2. Selezionare **Opzioni sviluppatore**.
3. Deselezionare l'opzione **Debug USB**.

Questo screenshot mostra la schermata **Opzioni sviluppatore** in un dispositivo che esegue Android 4.0.3:

[![Opzioni sviluppatore](set-up-device-for-development-images/developer-options-sml.png)](set-up-device-for-development-images/developer-options.png#lightbox)

### <a name="android-42-and-higher"></a>Android 4.2 e versioni successive

A partire da Android 4.2 e versioni successive, l'impostazione **Opzioni sviluppatore** è nascosta per impostazione predefinita. Per renderla disponibile, passare a **Impostazioni > Info sul telefono** e toccare l'elemento **Numero build** sette volte per visualizzare la scheda **Opzioni sviluppatore**:

[![Elemento Numero build](set-up-device-for-development-images/about-phone-sml.png)](set-up-device-for-development-images/about-phone.png#lightbox)

Quando la scheda **Opzioni sviluppatore** è disponibile in **Impostazioni > Sistema**, aprirla per visualizzare le impostazioni per sviluppatori:

[![Schermata Opzioni sviluppatore](set-up-device-for-development-images/developer3.png)](set-up-device-for-development-images/developer3.png#lightbox)

Questa è la posizione in cui è possibile abilitare le opzioni per sviluppatori, ad esempio il debug USB e la modalità Rimani attivo.

## <a name="install-usb-drivers"></a>Installare i driver USB

Questo passaggio non è necessario per macOS, perché è sufficiente connettere il dispositivo al Mac con un cavo USB.

Potrebbe essere necessario installare alcuni driver aggiuntivi prima che un computer Windows riconosca un dispositivo Android connesso tramite USB.

> [!NOTE]
> Questa è la procedura per configurare un dispositivo Google Nexus ed è disponibile per riferimento. La procedura per il dispositivo specifico in uso potrebbe essere leggermente diversa, anche se a grandi linee simile. Cercare informazioni sul dispositivo specifico in Internet in caso di problemi.

Eseguire l'applicazione **android.bat** nella directory **[Percorso installazione Android SDK]\tools**. Per impostazione predefinita, il programma di installazione di Xamarin.Android posiziona Android SDK nel percorso seguente in un computer Windows:

`C:\Users\[username]\AppData\Local\Android\android-sdk`

### <a name="download-the-usb-drivers"></a>Scaricare i driver USB

I dispositivi Google Nexus (fatta eccezione per Galaxy Nexus) richiedono il driver USB Google. Il driver per i dispositivi Galaxy Nexus viene [distribuito da Samsung](https://www.samsung.com/us/support/downloads/).
Tutti gli altri dispositivi Android devono usare il [driver USB fornito dal produttore](https://developer.android.com/tools/extras/oem-usb.html#Drivers).

Per installare il pacchetto **Google USB Driver** avviare Android SDK Manager ed espandere la cartella **Extras**, come nello screenshot seguente:

[![Pacchetto Google USB Driver selezionato](set-up-device-for-development-images/usbdriverpackage.png)](set-up-device-for-development-images/usbdriverpackage.png#lightbox)

Selezionare la casella **Google USB Driver** e fare clic sul pulsante **Install** (Installa).
I file del driver vengono scaricati nel percorso seguente:

`[Android SDK install path]\extras\google\usb\_driver`

Il percorso predefinito per un'installazione di Xamarin.Android è:

`C:\Users\[username]\AppData\Local\Android\android-sdk\extras\google\usb_driver`

### <a name="installing-the-usb-driver"></a>Installazione del driver USB

Dopo aver scaricato i driver USB, è necessario installarli.
Per installare i driver in Windows 7:

1. Connettere il dispositivo al computer con un cavo USB.

2. Fare clic con il pulsante destro del mouse sul computer nel desktop o in Esplora risorse e scegliere **Gestisci**.

3. Selezionare **Dispositivi** nel riquadro a sinistra.

4. Individuare ed espandere **Altri dispositivi** nel riquadro a destra.

5. Fare clic con il pulsante destro del mouse sul nome del dispositivo e scegliere **Aggiornamento software driver**.
    Verrà avviato l'Aggiornamento guidato hardware.

6. Selezionare **Cerca il software del driver nel computer** e fare clic su **Avanti**.

7. Fare clic su **Sfoglia** e individuare la cartella del driver USB (il driver USB Google si trova in **[Percorso installazione Android SDK]\extras\google\usb_driver**).

8. Fare clic su **Avanti** per installare il driver.

### <a name="installing-unverified-drivers-in-windows-8"></a>Installazione di driver non verificati in Windows 8

Potrebbero essere necessari alcuni passaggi aggiuntivi per installare un driver non verificato in Windows 8. La procedura seguente descrive come installare i driver per un dispositivo Galaxy Nexus:

1. **Accedere alle opzioni di avvio avanzate di Windows 8**: per questo passaggio è necessario riavviare il computer per accedere alle opzioni di avvio avanzate. Avviare un prompt dei comandi e riavviare il computer con il comando seguente:

    ```command
    shutdown.exe /r /o
    ```

2. **Connettere il dispositivo**: connettere il dispositivo al computer

3. **Avviare Gestione dispositivi**: eseguire **devmgmt.msc**. Il dispositivo dovrebbe essere elencato con un triangolo giallo su di esso.

4. **Installare i driver di dispositivo**: installare i driver di dispositivo, come descritto in precedenza.

## <a name="connect-the-device-to-the-computer"></a>Connettere il dispositivo al computer

Il passaggio finale prevede la connessione del dispositivo al computer. È possibile procedere in due modi:

- **Cavo USB**: questo è il modo più semplice e più comune. Collegare semplicemente il cavo USB nel dispositivo e quindi nel computer.

- **Wi-Fi**: è possibile connettere un dispositivo Android a un computer senza usare un cavo USB, tramite Wi-Fi. Questa tecnica è un po' più complessa, ma potrebbe essere utile quando il dispositivo è troppo lontano dal computer per essere costantemente collegato tramite cavo. La connessione tramite Wi-Fi verrà descritta nella sezione successiva.

### <a name="connecting-over-wifi"></a>Connessione tramite Wi-Fi

Per impostazione predefinita, [Android Debug Bridge](https://developer.android.com/tools/help/adb.html) (*ADB*) è configurato per comunicare con un dispositivo Android tramite USB. È possibile riconfigurarlo per l'uso di TCP/IP anziché USB. A tale scopo, sia il dispositivo che il computer devono trovarsi nella stessa rete Wi-Fi. Per configurare l'ambiente per eseguire il debug tramite Wi-Fi, eseguire questi passaggi dalla riga di comando:

1. Determinare l'indirizzo IP del dispositivo Android. Un modo per individuare l'indirizzo IP è controllare in **Impostazioni > Wi-Fi** e quindi toccare la rete Wi-Fi a cui è connesso il dispositivo. Verrà visualizzata una schermata di impostazioni che mostra informazioni sulla connessione di rete, simile allo screenshot seguente:

    ![Indirizzo IP](set-up-device-for-development-images/ip-settings.png)

    In alcune versioni di Android l'indirizzo IP non viene elencato, ma è disponibile in **Impostazioni > Info sul telefono > Stato**.

2. Connettere il dispositivo Android al computer tramite USB.

3. Riavviare quindi ADB in modo che usi TCP sulla porta 5555. Digitare il comando seguente da un prompt dei comandi:

    ```command
    adb tcpip 5555
    ```

    Dopo aver eseguito questo comando, il computer non potrà essere in ascolto dei dispositivi connessi tramite USB.

4. Scollegare il cavo USB che connette il dispositivo al computer.

5. Configurare ADB in modo che si connetta al dispositivo Android sulla porta specificata nel passaggio 1:

    ```command
    adb connect 192.168.1.28:5555
    ```

    Dopo l'esecuzione di questo comando, il dispositivo Android è connesso al computer tramite Wi-Fi.

Al termine delle operazioni di debug tramite Wi-Fi, è possibile reimpostare la modalità USB per ADB con il comando seguente:

```command
adb usb
```

È possibile chiedere ad ADB di elencare i dispositivi connessi al computer. Indipendentemente dalla modalità di connessione dei dispositivi, è possibile eseguire il comando seguente al prompt dei comandi per visualizzare i dispositivi connessi:

```command
adb devices
```

## <a name="summary"></a>Riepilogo

In questo articolo sono state illustrate le procedure per configurare un dispositivo Android per lo sviluppo abilitando il debug nel dispositivo. È stato anche descritto come connettere il dispositivo a un computer tramite USB o Wi-Fi.

## <a name="related-links"></a>Collegamenti correlati

- [Android Debug Bridge](https://developer.android.com/tools/help/adb.html)
- [Uso di dispositivi hardware](https://developer.android.com/tools/device.html)
- [Download di driver Samsung](https://www.samsung.com/us/support/downloads/)
- [Driver USB OEM](https://developer.android.com/tools/extras/oem-usb.html#Drivers)
- [Google USB Driver](https://developer.android.com/sdk/win-usb.html)
- [Sviluppatori XDA: Windows 8 - Problema del driver ADB/Fastboot risolto](https://forum.xda-developers.com/showthread.php?t=1583801)
