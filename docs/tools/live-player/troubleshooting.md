---
title: Risoluzione dei problemi
description: Problemi noti con Xamarin Player in tempo reale e su come correggerli.
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
ms.technology: xamarin-cross-platform
author: topgenorth
ms.author: toopge
ms.date: 05/17/2017
ms.openlocfilehash: f9d5d0e4a2217d48577c60940fb027b3a77416d2
ms.sourcegitcommit: 945df041e2180cb20af08b83cc703ecd1aedc6b0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2018
---
# <a name="troubleshooting"></a>Risoluzione dei problemi

![Funzionalità di anteprima](~/media/shared/preview.png)

In questo articolo vengono illustrati alcuni problemi comuni e le procedure per correggerli.


## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>Dispositivo mobile non connettersi dopo l'analisi del codice a barre (o l'inserimento del codice)

Si verifica quando il dispositivo mobile esegue Xamarin Player in tempo reale non è presente nella stessa rete del computer che esegue l'IDE. Verificare le seguenti informazioni:

- Verificare che il dispositivo e il computer siano nella stessa rete Wi-Fi.
  - Se il computer è connesso anche a una rete cablata, provare a scollegare il cavo.
- La rete potrebbe essere strettamente protetti (ad esempio, in alcune reti aziendali), il blocco delle porte necessarie da Xamarin Player in tempo reale.
- Chiudere l'app Xamarin Player in tempo reale e riavviarlo.


## <a name="error-while-trying-to-deploy-message-in-ide"></a>Messaggio "Errore durante il tentativo di distribuire" nell'IDE

**"IOException: Impossibile leggere dati dalla connessione del trasporto: blocca l'operazione su socket non bloccante"**

Questo errore si verifica spesso quando il dispositivo mobile che esegue Xamarin Player in tempo reale non è presente nella stessa rete del computer che esegue Visual Studio. Ciò accade spesso quando ci si connette a un dispositivo che è stata precedentemente abbinato correttamente.

* Verificare che il dispositivo e il computer siano nella stessa rete Wi-Fi.
* La rete potrebbe essere strettamente protetti (ad esempio, in alcune reti aziendali), il blocco delle porte necessarie da Xamarin Player in tempo reale. Le porte seguenti sono necessari per Xamarin Player in tempo reale:
  * 37847: accesso alla rete interna di 
  * 8090-accesso alla rete esterno

## <a name="manually-configure-device"></a>Configurare manualmente dispositivo

Se non è possibile connettersi al dispositivo tramite Wi-Fi è possibile tentare di configurare manualmente il dispositivo tramite il file di configurazione, con i passaggi seguenti:

**Passaggio 1: Aprire file di configurazione**

Accedi a cartella di dati dell'applicazione:

* Windows: **%userprofile%\AppData\Roaming**
* macOS: **~/Users/$USER/.config**

In questa cartella troverà **PlayerDeviceList.xml** se non esiste, è necessario crearne uno.

**Passaggio 2: Ottenere l'indirizzo IP**

Nell'app Xamarin Player in tempo reale, passare a **sulle > Test della connessione > Avvia Test connessione**.

Prendere nota dell'indirizzo IP, sarà necessario l'indirizzo IP elencato quando si configura il dispositivo.

**Passaggio 3: Ottenere codice di associazione**

All'interno della scelta di Xamarin Player Live **coppia** oppure **coppia nuovamente**, quindi premere **immettere manualmente**. Un codice numerico verrà visualizzato, è necessario aggiornare il file di configurazione.

**Passaggio 4: Generare GUID**

Passare a: https://www.guidgenerator.com/online-guid-generator.aspx e generare un nuovo guid e verificare che sia lettere maiuscole nel.


**Passaggio 5: Configurare i dispositivi**

Aprire la **PlayerDeviceList.xml** backup in un editor, ad esempio Visual Studio o Visual Studio Code. È necessario configurare manualmente il dispositivo in questo file. Per impostazione predefinita, il file deve contenere il seguente vuoto `Devices` (elemento XML):

```xml
<DeviceList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Devices>

</Devices>
</DeviceList>
```

**Aggiungere dispositivi iOS:**

```xml
<PlayerDevice>
<SecretCode>ENTER-PAIR-CODE-HERE</SecretCode>
<UniqueIdentifier>ENTER-GUID-HERE</UniqueIdentifier>
<Name>iPhone Player</Name>
<Platform>iOS</Platform>
<AndroidApiLevel>0</AndroidApiLevel>
<DebuggerEndPoint>ENTER-IP-HERE:37847</DebuggerEndPoint>
<HostEndPoint />
<NeedsAppInstall>false</NeedsAppInstall>
<IsSimulator>false</IsSimulator>
<SimulatorIdentifier />
<LastConnectTimeUtc>2018-01-08T20:36:03.9492291Z</LastConnectTimeUtc>
</PlayerDevice>
```


**Aggiungere un dispositivo Android:**

```xml
<PlayerDevice>
<SecretCode>ENTER-PAIR-CODE-HERE</SecretCode>
<UniqueIdentifier>ENTER-GUID-HERE</UniqueIdentifier>
<Name>Android Player</Name>
<Platform>Android</Platform>
<AndroidApiLevel>24</AndroidApiLevel>
<DebuggerEndPoint>ENTER-IP-HERE:37847</DebuggerEndPoint>
<HostEndPoint />
<NeedsAppInstall>false</NeedsAppInstall>
<IsSimulator>false</IsSimulator>
<SimulatorIdentifier />
<LastConnectTimeUtc>2018-01-08T20:34:42.2332328Z</LastConnectTimeUtc>
</PlayerDevice>
```

**Chiudere e riaprire Visual Studio.** Il dispositivo dovrebbe essere inclusa nell'elenco.


## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>Messaggio "tipo o spazio dei nomi non può essere trovato" nell'IDE

Verificare che è stato selezionato un **progetto di avvio** che corrisponde al tipo di dispositivo (iOS e Android) e che la configurazione corrispondente al tipo di dispositivo (ad es. **Eseguire il debug | iPhone simulatore** per iOS).

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>Messaggio "Costruttore sul tipo 'InterpretedXamarin.Forms.Button' non trovata" nel lettore

Alcune classi di sistema non possono essere sottoposto a override, ad esempio:

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>"Mainactivity: 'Resource.Layout' non contiene una definizione per 'Main'"

Questo errore si verifica per i progetti Android con interfacce utente definite nel file AXML.
File AXML non sono attualmente supportati in Xamarin Player in tempo reale.

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>Android barra degli strumenti e le schede, eseguire il rendering in modo non corretto con xamarin. Forms

I progetti Android di xamarin. Forms devono utilizzare "Toolbar.axml" e "Tabbar.axml" per i nomi dei file di layout pertinente. Il modello predefinito utilizza questi nomi. ridenominazione di essi causerà problemi di rendering.


Segnalare eventuali problemi aggiuntivi su [bugzilla](https://aka.ms/live-player-report-issue).


## <a name="related-links"></a>Collegamenti correlati

- [Limitazioni](~/tools/live-player/limitations.md)
- [Configurazione](~/tools/live-player/install.md)
