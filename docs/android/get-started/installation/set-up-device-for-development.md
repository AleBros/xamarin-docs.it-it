---
title: Configurare il dispositivo per lo sviluppo
description: Questo articolo illustra come installare un dispositivo Android fisico e connetterlo a un computer in modo che il dispositivo possa essere usato per eseguire applicazioni Xamarin.Android ed eseguirne il debug.
ms.prod: xamarin
ms.assetid: 9116A3AA-EA00-56AF-AE70-BAEEC045EF11
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 06/22/2018
ms.openlocfilehash: 72e0a2adc79796b3df7b6fb4eca62448f1a1a7a4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "79510731"
---
# <a name="set-up-device-for-development"></a>Configurare il dispositivo per lo sviluppo

_Questo articolo illustra come installare un dispositivo Android e connetterlo a un computer in modo che il dispositivo possa essere usato per eseguire applicazioni Xamarin.Android ed eseguirne il debug._

Al termine dei test in un emulatore Android, sarà utile visualizzare e testare le app in esecuzione in un dispositivo Android. È necessario abilitare il debug e connettere il dispositivo al computer.

Ognuno di questi passaggi verrà descritto più dettagliatamente nelle sezioni seguenti.

## <a name="enable-debugging-on-the-device"></a>Abilitare il debug nel dispositivo

Un dispositivo deve essere abilitato per il debug per testare un'applicazione Android.A device must be enabled for debugging in order to test an Android application. Le opzioni per gli sviluppatori su Android sono state nascoste per impostazione predefinita dalla versione 4.2 e abilitarle può variare in base alla versione di Android.

### <a name="android-90"></a>Android 9.0 e versioni successive

Per Android 9.0 e versioni successive, il debug è abilitato attenendosi alla seguente procedura:

1. Passare alla schermata **Impostazioni**.
2. Selezionare **Informazioni sul telefono** .
3. Tocca **Il numero di build** 7 volte fino a quando non sei uno **sviluppatore!** è visibile.

### <a name="android-80-and-android-81"></a>Android 8.0 e Android 8.1

1. Vai alla schermata **Impostazioni.**
2. Selezionare **Sistema**.
3. Seleziona **Informazioni sul telefono**
4. Tocca **Il numero di build** 7 volte fino a quando non sei uno **sviluppatore!** è visibile.

### <a name="android-71-and-lower"></a>Android 7.1 e versioni successive

1. Vai alla schermata **Impostazioni.**
2. Selezionare **Informazioni sul telefono**.
3. Tocca **Il numero di build** 7 volte fino a quando non sei uno **sviluppatore!** è visibile.

[![Schermata Opzioni sviluppatore su Android 9.0](set-up-device-for-development-images/build-version-sml.png)](set-up-device-for-development-images/build-version.png#lightbox)

### <a name="verify-that-usb-debugging-is-enabled"></a>Verificare che il debug USB sia abilitato

Dopo aver abilitato la modalità sviluppatore nel dispositivo, è necessario assicurarsi che il debug USB sia abilitato nel dispositivo. Questo varia anche in base alla versione di Android.

### <a name="android-90"></a>Android 9.0 e versioni successive

Passare a **Impostazioni > sistema > Opzioni avanzate > sviluppatore** e abilitare il debug **USB**.

### <a name="android-80-and-android-81"></a>Android 8.0 e Android 8.1

Passare a **Impostazioni > Sistema > Opzioni sviluppatore** e abilitare il debug **USB**.

### <a name="android-71-and-lower"></a>Android 7.1 e versioni successive

Passare a **Impostazioni > Opzioni sviluppatore** e abilitare **il debug USB**.

Quando la scheda **Opzioni sviluppatore** è disponibile in **Impostazioni > Sistema**, aprirla per visualizzare le impostazioni per sviluppatori:

[![Schermata Opzioni sviluppatore su Android 9.0](set-up-device-for-development-images/usb-debugging-sml.png)](set-up-device-for-development-images/usb-debugging.png#lightbox)

Questa è la posizione in cui è possibile abilitare le opzioni per sviluppatori, ad esempio il debug USB e la modalità Rimani attivo.

## <a name="connect-the-device-to-the-computer"></a>Collegare il dispositivo al computer

Il passaggio finale prevede la connessione del dispositivo al computer. Il modo più semplice e affidabile è farlo tramite USB.

Verrà richiesto di considerare attendibile il computer nel dispositivo se non è stato utilizzato per il debug in precedenza. È inoltre possibile **selezionare Consenti sempre da questo computer** per evitare di richiedere questa richiesta ogni volta che si connette il dispositivo.

![](set-up-device-for-development-images/trust-computer-for-usb-debugging.png "Google USB")

## <a name="alternate-connection-via-wifi"></a>Connessione alternativa tramite Wifi

È possibile collegare un dispositivo Android a un computer senza utilizzare un cavo USB, tramite WiFi. Questa tecnica richiede più sforzo, ma potrebbe essere utile quando il dispositivo è troppo lontano dal computer per rimanere costantemente collegato tramite cavo. 

### <a name="connecting-over-wifi"></a>Connessione tramite Wi-Fi

Per impostazione predefinita, [Android Debug Bridge](https://developer.android.com/tools/help/adb.html) (*ADB*) è configurato per comunicare con un dispositivo Android tramite USB. È possibile riconfigurarlo per l'uso di TCP/IP anziché USB. A tale scopo, sia il dispositivo che il computer devono trovarsi nella stessa rete Wi-Fi. Per configurare l'ambiente per eseguire il debug tramite WiFi, effettuare le seguenti operazioni dalla riga di comando:

1. Determinare l'indirizzo IP del dispositivo Android. Un modo per scoprire l'indirizzo IP è quello di guardare in **Impostazioni > Rete & Internet > Wi-Fi**, quindi toccare la rete WiFi a cui è collegato il dispositivo, quindi toccare **Avanzate**. Si aprirà un menu a discesa che mostra le informazioni sulla connessione di rete, simile a quello che si vede nella schermata qui sotto:

    [![Indirizzo IP](set-up-device-for-development-images/ip-settings-sml.png)](set-up-device-for-development-images/ip-settings.png#lightbox)

    Su alcune versioni di Android l'indirizzo IP non sarà elencato lì, ma può essere trovato invece in **Impostazioni > Informazioni su telefono > Stato**.

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

    Al termine di questo comando, il dispositivo Android è collegato al computer tramite WiFi.

    Quando hai finito di eseguire il debug tramite WiFi, è possibile ripristinare ADB in modalità USB con il seguente comando:
    
    ```command
    adb usb
    ```
    
    È possibile richiedere a ADB di elencare i dispositivi connessi al computer. Indipendentemente dalla modalità di connessione dei dispositivi, è possibile eseguire il comando seguente al prompt dei comandi per visualizzare i dispositivi connessi:
    
    ```command
    adb devices
    ```

## <a name="troubleshooting"></a>Risoluzione dei problemi

In alcuni casi è possibile che il dispositivo non sia in grado di connettersi al computer. In questo caso è possibile verificare che i driver USB siano installati.

## <a name="install-usb-drivers"></a>Installare i driver USB

Questo passaggio non è necessario per macOS; è sufficiente collegare il dispositivo al Mac con un cavo USB.

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

Seleziona la casella **Driver USB Di Google** e fai clic sul pulsante Applica **modifiche.**
I file del driver vengono scaricati nel percorso seguente:

`[Android SDK install path]\extras\google\usb\_driver`

Il percorso predefinito per un'installazione di Xamarin.Android è:

`C:\Users\[username]\AppData\Local\Android\android-sdk\extras\google\usb_driver`

### <a name="installing-the-usb-driver"></a>Installazione del driver USB

Dopo aver scaricato i driver USB, è necessario installarli.
Per installare i driver in Windows 7:

1. Connettere il dispositivo al computer con un cavo USB.

2. Fare clic con il pulsante destro del mouse sul computer dal desktop o in Esplora risorse, quindi scegliere **Gestisci** .

3. Selezionare **Dispositivi** nel riquadro a sinistra.

4. Individuare ed espandere **Altri dispositivi** nel riquadro a destra.

5. Fare clic con il pulsante destro del mouse sul nome del dispositivo e scegliere **Aggiornamento software driver**.
    Verrà avviato l'Aggiornamento guidato hardware.

6. Selezionare **Sfoglia il computer per il software del driver** e fare clic su **Avanti** .

7. Fai clic su **Sfoglia** e individua la cartella del driver USB (il driver USB di Google si trova nel percorso di installazione di **[Android SDK usb_driver]**

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
