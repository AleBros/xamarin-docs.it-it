---
title: Set Up Device for Development (Configurare il dispositivo per lo sviluppo)
description: Questo articolo illustra come installare un dispositivo Android fisico e connetterlo a un computer in modo che il dispositivo possa essere usato per eseguire applicazioni Xamarin.Android ed eseguirne il debug.
ms.prod: xamarin
ms.assetid: 9116A3AA-EA00-56AF-AE70-BAEEC045EF11
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 72e0a2adc79796b3df7b6fb4eca62448f1a1a7a4
ms.sourcegitcommit: 997f7b6a1a1bc50b98c3ca5bbc75d6875ba2ae9a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2020
ms.locfileid: "79510731"
---
# <a name="set-up-device-for-development"></a>Set Up Device for Development (Configurare il dispositivo per lo sviluppo)

_Questo articolo illustra come installare un dispositivo Android e connetterlo a un computer in modo che il dispositivo possa essere usato per eseguire applicazioni Xamarin.Android ed eseguirne il debug._

Al termine dei test in un emulatore Android, sarà utile visualizzare e testare le app in esecuzione in un dispositivo Android. Sarà necessario abilitare il debug e connettere il dispositivo al computer.

Ognuno di questi passaggi verrà descritto più dettagliatamente nelle sezioni seguenti.

## <a name="enable-debugging-on-the-device"></a>Abilitare il debug nel dispositivo

Per testare un'applicazione Android, è necessario abilitare il debug di un dispositivo. Le opzioni per gli sviluppatori in Android sono state nascoste per impostazione predefinita dalla versione 4,2 e l'abilitazione può variare in base alla versione di Android.

### <a name="android-90"></a>Android 9.0 +

Per Android 9,0 e versioni successive, il debug viene attivato attenendosi alla procedura seguente:

1. Passare alla schermata **Impostazioni**.
2. Selezionare **informazioni su telefono** .
3. Toccare **numero build** 7 volte fino a quando **non si è uno sviluppatore.** è visibile.

### <a name="android-80-and-android-81"></a>Android 8,0 e Android 8,1

1. Passare alla schermata **Impostazioni** .
2. Selezionare **sistema**.
3. Selezionare **informazioni sul telefono**
4. Toccare **numero build** 7 volte fino a quando **non si è uno sviluppatore.** è visibile.

### <a name="android-71-and-lower"></a>Android 7,1 e versioni precedenti

1. Passare alla schermata **Impostazioni** .
2. Selezionare **informazioni su telefono**.
3. Toccare **numero build** 7 volte fino a quando **non si è uno sviluppatore.** è visibile.

[schermata delle opzioni di ![Developer in Android 9,0](set-up-device-for-development-images/build-version-sml.png)](set-up-device-for-development-images/build-version.png#lightbox)

### <a name="verify-that-usb-debugging-is-enabled"></a>Verificare che il debug USB sia abilitato

Dopo aver abilitato la modalità sviluppatore nel dispositivo, è necessario assicurarsi che il debug USB sia abilitato nel dispositivo. Questo può variare anche in base alla versione di Android.

### <a name="android-90"></a>Android 9.0 +

Passare a **impostazioni > sistema > opzioni avanzate > Developer** e abilitare il **debug USB**.

### <a name="android-80-and-android-81"></a>Android 8,0 e Android 8,1

Passare a **impostazioni > opzioni di sistema > per sviluppatori** e abilitare il **debug USB**.

### <a name="android-71-and-lower"></a>Android 7,1 e versioni precedenti

Passare a **impostazioni > opzioni** per gli sviluppatori e abilitare il **debug USB**.

Quando la scheda **Opzioni sviluppatore** è disponibile in **Impostazioni > Sistema**, aprirla per visualizzare le impostazioni per sviluppatori:

[schermata delle opzioni di ![Developer in Android 9,0](set-up-device-for-development-images/usb-debugging-sml.png)](set-up-device-for-development-images/usb-debugging.png#lightbox)

Questa è la posizione in cui è possibile abilitare le opzioni per sviluppatori, ad esempio il debug USB e la modalità Rimani attivo.

## <a name="connect-the-device-to-the-computer"></a>Connettere il dispositivo al computer

Il passaggio finale prevede la connessione del dispositivo al computer. Il modo più semplice e affidabile consiste nell'eseguire questa operazione su USB.

Se non è stato usato per il debug, si riceverà una richiesta di attendibilità del computer nel dispositivo. È anche possibile selezionare **Consenti sempre da questo computer** per evitare la richiesta di questa richiesta ogni volta che si connette il dispositivo.

![](set-up-device-for-development-images/trust-computer-for-usb-debugging.png "Google USB")

## <a name="alternate-connection-via-wifi"></a>Connessione alternativa tramite WiFi

È possibile connettere un dispositivo Android a un computer senza usare un cavo USB tramite Wi-Fi. Questa tecnica richiede una maggiore quantità di risorse, ma potrebbe essere utile quando il dispositivo è troppo lontano dal computer per restare costantemente collegato tramite cavo. 

### <a name="connecting-over-wifi"></a>Connessione tramite Wi-Fi

Per impostazione predefinita, [Android Debug Bridge](https://developer.android.com/tools/help/adb.html) (*ADB*) è configurato per comunicare con un dispositivo Android tramite USB. È possibile riconfigurarlo per l'uso di TCP/IP anziché USB. A tale scopo, sia il dispositivo che il computer devono trovarsi nella stessa rete Wi-Fi. Per configurare l'ambiente per eseguire il debug tramite WiFi, completare i passaggi seguenti dalla riga di comando:

1. Determinare l'indirizzo IP del dispositivo Android. Un modo per trovare l'indirizzo IP consiste nel visualizzare **le impostazioni > rete & internet > Wi-Fi**, quindi toccare la rete Wi-Fi a cui è connesso il dispositivo e quindi toccare **Avanzate**. Verrà aperto un elenco a discesa che mostra le informazioni sulla connessione di rete, in modo simile a quanto illustrato nella schermata seguente:

    [Indirizzo IP ![](set-up-device-for-development-images/ip-settings-sml.png)](set-up-device-for-development-images/ip-settings.png#lightbox)

    In alcune versioni di Android l'indirizzo IP non viene elencato, ma è disponibile in **Impostazioni > Info sul telefono > Stato**.

2. Connettere il dispositivo Android al computer tramite USB.

3. Riavviare quindi ADB in modo che usi TCP sulla porta 5555. Al prompt dei comandi digitare il comando seguente:

    ```command
    adb tcpip 5555
    ```

    Dopo aver eseguito questo comando, il computer non potrà essere in ascolto dei dispositivi connessi tramite USB.

4. Scollegare il cavo USB che connette il dispositivo al computer.

5. Configurare ADB in modo che si connetta al dispositivo Android sulla porta specificata nel passaggio 1:

    ```command
    adb connect 192.168.1.28:5555
    ```

    Al termine del comando, il dispositivo Android è connesso al computer tramite Wi-Fi.

    Al termine del debug tramite Wi-Fi, è possibile reimpostare ADB sulla modalità USB con il comando seguente:
    
    ```command
    adb usb
    ```
    
    È possibile richiedere ADB per elencare i dispositivi connessi al computer. Indipendentemente dalla modalità di connessione dei dispositivi, è possibile eseguire il comando seguente al prompt dei comandi per visualizzare i dispositivi connessi:
    
    ```command
    adb devices
    ```

## <a name="troubleshooting"></a>Risoluzione dei problemi

In alcuni casi è possibile che il dispositivo non sia in grado di connettersi al computer. In questo caso è consigliabile verificare che i driver USB siano installati.

## <a name="install-usb-drivers"></a>Installare i driver USB

Questo passaggio non è necessario per macOS; è sufficiente connettere il dispositivo al Mac con un cavo USB.

Potrebbe essere necessario installare alcuni driver aggiuntivi prima che un computer Windows riconosca un dispositivo Android connesso tramite USB.

> [!NOTE]
> Questa è la procedura per configurare un dispositivo Google Nexus ed è disponibile per riferimento. La procedura per il dispositivo specifico in uso potrebbe essere leggermente diversa, anche se a grandi linee simile. Cercare informazioni sul dispositivo specifico in Internet in caso di problemi.

Eseguire l'applicazione **android.bat** nella directory **[Percorso installazione Android SDK]\tools**. Per impostazione predefinita, il programma di installazione di Xamarin.Android posiziona Android SDK nel percorso seguente in un computer Windows:

`C:\Users\[username]\AppData\Local\Android\android-sdk`

### <a name="download-the-usb-drivers"></a>Scaricare i driver USB

I dispositivi Google Nexus (fatta eccezione per Galaxy Nexus) richiedono il driver USB Google. Il driver per i dispositivi Galaxy Nexus viene [distribuito da Samsung](https://www.samsung.com/us/support/downloads/).
Tutti gli altri dispositivi Android devono usare il [driver USB fornito dal produttore](https://developer.android.com/tools/extras/oem-usb.html#Drivers).

Per installare il pacchetto **Google USB Driver** avviare Android SDK Manager ed espandere la cartella **Extras**, come nello screenshot seguente:

![](set-up-device-for-development-images/google-usb-driver.png "Google USB driver selected")

Selezionare la casella **Google USB driver** , quindi fare clic sul pulsante **Apply Changes** .
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

## <a name="summary"></a>Riepilogo

In questo articolo sono state illustrate le procedure per configurare un dispositivo Android per lo sviluppo abilitando il debug nel dispositivo. È stato anche descritto come connettere il dispositivo a un computer tramite USB o Wi-Fi.

## <a name="related-links"></a>Collegamenti correlati

- [Android Debug Bridge](https://developer.android.com/tools/help/adb.html)
- [Uso di dispositivi hardware](https://developer.android.com/tools/device.html)
- [Download di driver Samsung](https://www.samsung.com/us/support/downloads/)
- [Driver USB OEM](https://developer.android.com/tools/extras/oem-usb.html#Drivers)
- [Google USB Driver](https://developer.android.com/sdk/win-usb.html)
- [Sviluppatori XDA: Windows 8 - Problema del driver ADB/Fastboot risolto](https://forum.xda-developers.com/showthread.php?t=1583801)
