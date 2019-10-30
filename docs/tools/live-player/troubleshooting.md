---
title: Risoluzione dei problemi Xamarin Live Player
description: In questo documento vengono descritti i problemi noti relativi alla Xamarin Live Player e alle potenziali correzioni. Vengono illustrati i problemi di connessione, i problemi di configurazione e altro ancora.
ms.prod: xamarin
ms.assetid: 29A97ADA-80E0-40A1-8B26-C68FFABE7D26
author: davidortinau
ms.author: daortin
ms.date: 06/13/2019
ms.openlocfilehash: d51241bee5f4ddc06032006071fa8296be37f2fb
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73005935"
---
# <a name="troubleshooting-xamarin-live-player"></a>Risoluzione dei problemi Xamarin Live Player

![Funzionalità di anteprima](~/media/shared/preview.png)

> [!WARNING]
> L'anteprima del Xamarin Live Player è terminata. L'app non è più disponibile. Le istruzioni riportate di seguito vengono fornite ai clienti che continuano a usare l'anteprima con Visual Studio 2017.

> [!TIP]
> È possibile usare il visualizzatore [Anteprima XAML](~/xamarin-forms/xaml/xaml-previewer/index.md) in Visual Studio 2019 o Visual Studio per Mac per visualizzare le progettazioni dello schermo durante la modifica.

Questo articolo illustra le limitazioni di Live Player e alcuni problemi comuni con i passaggi per correggerli.

## <a name="limitations-of-xamarin-live-player"></a>Limitazioni di Xamarin Live Player

### <a name="ide-requirements"></a>Requisiti dell'IDE

L'anteprima di Live Player è disponibile solo in Visual Studio 2017.

### <a name="device-requirements"></a>Requisiti del dispositivo

L'app Xamarin Live Player supporta i dispositivi Android seguenti:

- Android 4,2 o versione successiva.
- ARM-v7a, ARM-V8A, ARM64-V8A, x86 o x86_64 Processor.

### <a name="ios-limitations"></a>limitazioni di iOS

Live Player non è disponibile per iOS.

### <a name="xamarinforms-limitations"></a>Limitazioni di Novell. Forms

- Renderer personalizzati non supportati.
- Gli effetti non sono supportati.
- I controlli personalizzati con proprietà associabili personalizzate non sono supportati.
- Le risorse incorporate non sono supportate, ad esempio incorporando immagini o altre risorse in una libreria di classi portabile (PCL).
- I framework MVVM di terze parti non sono supportati (ad esempio, Prisma, MVVM Cross, MVVM Light e così via.

### <a name="other-project-type-limitations"></a>Altre limitazioni del tipo di progetto

- Live Player non è destinato a progetti Android nativi (che usano il codice XML Android per l'interfaccia utente).

### <a name="miscellaneous-limitations"></a>Limitazioni varie

- Supporto limitato per la reflection (attualmente interessa alcuni NuGet più diffusi, ad esempio SQLite e Json.NET). È possibile che altri NuGet siano ancora supportati.
- Non è possibile eseguire l'override di alcune classi di sistema (ad esempio, non è possibile implementare una sottoclasse).
- Alcune funzionalità della piattaforma che richiedono il provisioning non funzionano nell'app Xamarin Live Player (ma sono state configurate per operazioni comuni come l'accesso alla raccolta foto).
- Le destinazioni personalizzate e le istruzioni di compilazione vengono ignorate. Ad esempio, non è possibile incorporare strumenti come Fody, refitting, AutoFac e automapper.
- F#i progetti non sono supportati
- Gli scenari avanzati con interfacce e classi generiche personalizzate potrebbero non essere supportati.

## <a name="mobile-device-does-not-connect-after-scanning-barcode-or-entering-code"></a>Il dispositivo mobile non si connette dopo la scansione del codice a barre (o l'immissione del codice)

Si verifica quando il dispositivo mobile in cui è in esecuzione Xamarin Live Player non si trova nella stessa rete del computer in cui è in esecuzione l'IDE. Vedere quanto segue:

- Verificare che sia il dispositivo che il computer si trovino nella stessa rete Wi-Fi.
  - Se il computer è connesso anche a una rete cablata, provare a scollegare la connessione cablata.
- È possibile che la rete sia strettamente protetta, ad esempio alcune reti aziendali, bloccando le porte necessarie per Xamarin Live Player.
- Chiudere l'app Xamarin Live Player e riavviarla.

## <a name="error-while-trying-to-deploy-message-in-ide"></a>Messaggio "errore durante il tentativo di distribuzione" nell'IDE

**"IOException: Impossibile leggere i dati dalla connessione di trasporto: l'operazione sul socket non di blocco verrebbe bloccata"**

Questo errore si verifica spesso quando il dispositivo mobile che esegue Xamarin Live Player non si trova nella stessa rete del computer in cui è in esecuzione Visual Studio. Questo problema si verifica spesso quando ci si connette a un dispositivo che in precedenza è stato associato correttamente.

- Verificare che sia il dispositivo che il computer si trovino nella stessa rete Wi-Fi.
- È possibile che la rete sia strettamente protetta, ad esempio alcune reti aziendali, bloccando le porte necessarie per Xamarin Live Player. Per il Xamarin Live Player sono necessarie le seguenti porte:
  - 37847: accesso alla rete interna 
  - 8090: accesso alla rete esterna

## <a name="manually-configure-device"></a>Configurare manualmente il dispositivo

Se non è possibile connettersi al dispositivo tramite Wi-Fi, è possibile provare a configurare manualmente il dispositivo tramite il file di configurazione, seguendo questa procedura:

**Passaggio 1: aprire il file di configurazione**

Passare alla cartella di dati dell'applicazione:

- Windows: **%USERPROFILE%\AppData\Roaming**
- macOS: **~/utenti/$User/.config**

In questa cartella si troverà **PlayerDeviceList. XML** , se non esiste, sarà necessario crearne uno.

**Passaggio 2: ottenere l'indirizzo IP**

Nell'app Xamarin Live Player passare a **informazioni su > test di connessione > avvia test di connessione**.

Prendere nota dell'indirizzo IP. sarà necessario l'indirizzo IP elencato quando si configura il dispositivo.

**Passaggio 3: ottenere il codice di associazione**

All'interno del Xamarin Live Player toccare di nuovo **coppia** o **coppia**, quindi premere **invio manualmente**. Verrà visualizzato un codice numerico, che sarà necessario aggiornare il file di configurazione.

**Passaggio 4: generare il GUID**

Vai a: https://www.guidgenerator.com/online-guid-generator.aspx e genera un nuovo GUID e assicurati che il maiuscolo sia on.

**Passaggio 5: configurare il dispositivo**

Aprire **PlayerDeviceList. XML** in un editor, ad esempio Visual Studio o Visual Studio Code. È necessario configurare manualmente il dispositivo in questo file. Per impostazione predefinita, il file deve contenere l'elemento XML `Devices` vuoto seguente:

```xml
<DeviceList xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema">
<Devices>

</Devices>
</DeviceList>
```

**Aggiungi dispositivo Android:**

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

**Chiudere e riaprire Visual Studio.** Il dispositivo dovrebbe essere visualizzato nell'elenco.

## <a name="type-or-namespace-cannot-be-found-message-in-ide"></a>Messaggio "tipo o spazio dei nomi non trovato" nell'IDE

Verificare di aver selezionato un **progetto di avvio** corrispondente al tipo di dispositivo, ad esempio Android) e che la configurazione corrisponda a quel tipo di dispositivo (ad esempio **Eseguire il debug** per Android.

## <a name="constructor-on-type-interpretedxamarinformsbutton-not-found-message-in-player"></a>"Costruttore sul tipo ' InterpretedXamarin. Forms. Button ' non trovato" nel lettore

Non è possibile eseguire l'override di alcune classi di sistema, ad esempio:

```csharp
public class SomeCustomButton : Xamarin.Forms.Button { ... }
```

## <a name="mainactivitycs-resourcelayout-does-not-contain-a-definition-for-main"></a>"MainActivity.cs:' Resource. layout ' non contiene una definizione per ' Main '"

Questo errore si verifica per i progetti Android con interfacce utente definite nei file AXML.
I file AXML non sono attualmente supportati in Xamarin Live Player.

### <a name="android-toolbar-and-tabs-render-incorrectly-using-xamarinforms"></a>Il rendering della barra degli strumenti e delle schede Android non è corretto con Novell. Forms

I progetti Android di Novell. Forms devono usare "Toolbar. aXML" e "Tabby. aXML" per i nomi dei file di layout pertinenti. Il modello predefinito usa questi nomi; la ridenominazione provocherà problemi di rendering.

## <a name="related-links"></a>Collegamenti correlati

- [Configurazione](~/tools/live-player/install.md)
