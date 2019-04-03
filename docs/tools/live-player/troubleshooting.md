---
title: Risoluzione dei problemi di Xamarin Live Player
description: Questo documento descrive problemi noti di Xamarin Live Player e potenziali correzioni. Illustra i problemi di connessione, problemi di configurazione e altro ancora.
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
author: lobrien
ms.author: laobri
ms.date: 08/08/2018
ms.openlocfilehash: 5eb0dcead230e0bb2e7d99241e5d8e5a4115f838
ms.sourcegitcommit: c4be32ef914465e808d89767c4d5ee72afe93cc6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2019
ms.locfileid: "58855263"
---
# <a name="troubleshooting-xamarin-live-player"></a>Risoluzione dei problemi di Xamarin Live Player

![Funzionalità di anteprima](~/media/shared/preview.png)

> [!NOTE]
> Live Player anteprima è disponibile solo in Visual Studio 2017.

Questo articolo illustra le limitazioni di Live Player e alcuni problemi comuni con i passaggi per correggerli.

## <a name="limitations-of-xamarin-live-player"></a>Limitazioni di Xamarin Live Player

### <a name="ide-requirements"></a>Requisiti per gli IDE

L'anteprima di Windows Media Player in tempo reale è disponibile solo in Visual Studio 2017.

### <a name="device-requirements"></a>Requisiti del dispositivo

L'app Xamarin Live Player supporta i dispositivi Android seguenti:

- Android 4.2 o versione successiva.
- ARM-v7a, v8a a ARM, ARM64-v8a, x86 o processore x86_64.

### <a name="ios-limitations"></a>limitazioni di iOS

Live Player non è disponibile per iOS.

### <a name="xamarinforms-limitations"></a>Limitazioni di xamarin. Forms

- Renderer personalizzati non sono supportati.
- Gli effetti non sono supportati.
- Controlli personalizzati con proprietà associabili personalizzati non sono supportati.
- Le risorse incorporate non supportate (ie. l'incorporamento di immagini o altre risorse in una libreria di classi Portabile).
- Framework MVVM di terze parti non sono supportati (ad esempio, Prism, Mvvm Cross, Mvvm Light e così via).

### <a name="other-project-type-limitations"></a>Altre limitazioni del tipo di progetto

- Live Player non è destinato a progetti Android nativi (che usano Android XML per l'interfaccia utente).

### <a name="miscellaneous-limitations"></a>Varie limitazioni

- Supporto limitato per la reflection (attualmente influisce su alcuni pacchetti NuGet più diffusi, come Json.NET e SQLite). Altri pacchetti NuGet potrebbe ancora essere supportata.
- Impossibile eseguire l'override di alcune classi di sistema (ad esempio, è Impossibile implementare una sottoclasse).
- Non è possibile usare alcune funzionalità della piattaforma che richiedono il provisioning nell'app Xamarin Live Player (tuttavia è stato configurato per le operazioni comuni quale l'accesso alla raccolta di foto).
- Istruzioni di compilazione e destinazioni personalizzate vengono ignorate. Ad esempio, non sarà possibile incorporarle strumenti come Fody, Riadatta, AutoFac e AutoMapper.
- F#progetti non sono supportati
- Scenari avanzati con le interfacce e classi generiche personalizzate potrebbero non essere supportati.

## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>Dispositivo mobile non si connette dopo l'analisi del codice a barre (o l'inserimento del codice)

Si verifica quando il dispositivo mobile in esecuzione di Xamarin Live Player non è presente nella stessa rete del computer che esegue l'IDE. Consultare quanto segue:

- Verificare che sia il dispositivo e il computer siano nella stessa rete Wi-Fi.
  - Se il computer è connesso anche a una rete cablata, provare a scollegare la connessione cablata.
- La rete possa essere protetti in modo sicuro (ad esempio, alcune reti aziendali), bloccare le porte necessarie per Xamarin Live Player.
- Chiudere l'app Xamarin Live Player e riavviarlo.

## <a name="error-while-trying-to-deploy-message-in-ide"></a>Messaggio "Errore durante il tentativo di distribuire" nell'IDE

**"IOException: Impossibile leggere i dati dalla connessione del trasporto: Operazione su socket non bloccante bloccherà"**

Questo errore si verifica spesso quando il dispositivo mobile in esecuzione di Xamarin Live Player non è presente nella stessa rete del computer che esegue Visual Studio. Ciò accade spesso quando ci si connette a un dispositivo che in precedenza è stato associato correttamente.

* Verificare che sia il dispositivo e il computer siano nella stessa rete Wi-Fi.
* La rete possa essere protetti in modo sicuro (ad esempio, alcune reti aziendali), bloccare le porte necessarie per Xamarin Live Player. Le porte seguenti sono necessari per Xamarin Live Player:
  * 37847-errore accesso alla rete interno 
  * 8090 – accesso alla rete esterno

## <a name="manually-configure-device"></a>Configurare manualmente i dispositivi

Se non è possibile connettersi al dispositivo tramite Wi-Fi per provare a configurare manualmente il dispositivo tramite il file di configurazione con i passaggi seguenti:

**Passaggio 1: Apri file di configurazione**

Passare alla cartella di dati dell'applicazione:

* Windows: **%userprofile%\AppData\Roaming**
* macOS: **~/Users/$USER/.config**

In questa cartella troveranno **PlayerDeviceList.xml** se non esiste, è necessario crearne uno.

**Passaggio 2: Ottenere l'indirizzo IP**

Nell'app Xamarin Live Player, passare a **sulle > Test connessione > Avvia il Test della connessione**.

Prendere nota dell'indirizzo IP, è necessario l'indirizzo IP elencato quando si configura il dispositivo.

**Passaggio 3: Ottenere codice di associazione**

All'interno della scelta di Xamarin Live Player **coppia** oppure **coppia nuovamente**, quindi premere **Immetti manualmente**. Un codice numerico verrà visualizzato, è necessario aggiornare il file di configurazione.

**Passaggio 4: Genera GUID**

Passare a: https://www.guidgenerator.com/online-guid-generator.aspx e generare un nuovo guid e assicurarsi che sia lettere maiuscole in.

**Passaggio 5: Configurare il dispositivo**

Aprire il **PlayerDeviceList.xml** backup in un editor, ad esempio Visual Studio o Visual Studio Code. È necessario configurare manualmente il dispositivo in questo file. Per impostazione predefinita, il file deve contenere il seguente vuoto `Devices` (elemento XML):

```xml
<DeviceList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Devices>

</Devices>
</DeviceList>
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

**Chiudere e riaprire Visual Studio.** Il dispositivo verranno visualizzate nell'elenco.

## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>Messaggio "tipo o spazio dei nomi non è stata trovata" nell'IDE

Verificare che è stata selezionata una **progetto di avvio** che corrisponde al tipo di dispositivo (ad es. Android) e che la configurazione corrisponda a quel tipo di dispositivo (ad es. **Eseguire il debug** per Android).

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>Messaggio "Konstruktor typu 'InterpretedXamarin.Forms.Button' non trovata" nel lettore

Alcune classi di sistema non possono essere sottoposto a override, ad esempio:

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>"MainActivity.cs: "Resource.Layout' non contiene una definizione per 'Main'"

Questo errore si verifica per i progetti Android con le interfacce utente definite nei file AXML.
File AXML non sono attualmente supportati in Xamarin Live Player.

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>Android sulla barra degli strumenti e le schede, eseguire il rendering in modo non corretto con xamarin. Forms

I progetti Android di xamarin. Forms è necessario usare "Toolbar.axml" e "Tabbar.axml" per i nomi dei file di layout pertinenti. Il modello predefinito utilizza questi nomi. ridenominazione di essi causerà problemi di rendering.

## <a name="related-links"></a>Collegamenti correlati

- [Configurazione](~/tools/live-player/install.md)
- [Campioni da utilizzare con Live Player](https://developer.xamarin.com/samples/xamarin-live-player/all/)