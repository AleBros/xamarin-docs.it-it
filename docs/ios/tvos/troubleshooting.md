---
title: Risoluzione dei problemi relativi alle app tvOS compilate con Xamarin
description: Questo articolo fornisce vari suggerimenti per la risoluzione dei problemi durante lo sviluppo di un'app tvOS compilata con Xamarin. Vengono descritti i problemi noti e gli errori specifici.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/16/2017
ms.openlocfilehash: 11ac6289b7d2f278f534f5a65679754d212b5067
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030523"
---
# <a name="troubleshooting-tvos-apps-built-with-xamarin"></a>Risoluzione dei problemi relativi alle app tvOS compilate con Xamarin

_Questo articolo illustra i problemi che possono verificarsi durante l'uso del supporto tvOS di Xamarin._

<a name="Known-Issues" />

## <a name="known-issues"></a>Problemi noti

La versione corrente del supporto tvOS di Xamarin presenta i seguenti problemi noti:

- **Mono Framework** : crittografia mono 4,3. ProtectedData non riesce a decrittografare i dati da mono 4,2. Di conseguenza, i pacchetti NuGet non vengono ripristinati con l'errore `Data unprotection failed` quando viene configurata un'origine NuGet protetta.
  - **Soluzione alternativa** : in Visual Studio per Mac è necessario aggiungere tutte le origini dei pacchetti NuGet che usano l'autenticazione della password prima di ritentare il ripristino dei pacchetti.
- **Visual Studio per Mac w/ F# componente aggiuntivo** : errore durante la creazione di F# un modello Android in Windows. Questa operazione dovrebbe continuare a funzionare correttamente in Mac.
- **Xamarin.Mac** : quando si esegue il progetto di modello unificato Xamarin.Mac con il Framework di destinazione impostato su `Unsupported`, è possibile che venga visualizzata la `Could not connect to the debugger` popup.
  - **Potenziale soluzione alternativa** : eseguire il downgrade della versione di mono Framework disponibile nel canale stabile.
- **Xamarin Visual studio & Xamarin.iOS** : quando si distribuiscono applicazioni WatchKit in Visual Studio, è possibile che venga visualizzato l'errore `The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist`.

Segnalare eventuali bug trovati su [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

## <a name="troubleshooting"></a>Troubleshooting

Le sezioni seguenti elencano alcuni problemi noti che possono verificarsi quando si usa tvOS 9 con Xamarin.tvOS e la soluzione a questi problemi:

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>Eseguibile non valido: il file eseguibile non contiene bitcode

Quando si tenta di inviare un'app Xamarin.tvOS all'App Store di Apple TV, è possibile che venga ricevuto un messaggio di errore nel formato _"eseguibile non valido. il file eseguibile non contiene bitcode"_ .

Per risolvere questo problema, eseguire le operazioni seguenti:

1. In Visual Studio per Mac fare clic con il pulsante destro del mouse sul file di progetto Xamarin.tvOS nella **Esplora soluzioni** e scegliere **Opzioni**.
2. Selezionare **compilazione tvOS** e assicurarsi che la configurazione di **rilascio** sia la seguente: 

    [![](troubleshooting-images/ts01.png "Select tvOS Build options")](troubleshooting-images/ts01.png#lightbox)
3. Aggiungere `--bitcode=asmonly` al campo **argomenti aggiuntivi di mTouch** e fare clic sul pulsante **OK** .
4. Ricompilare l'app nella configurazione di **rilascio** .

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>Verifica per verificare se l'app tvOS contiene bitcode

Per verificare che la compilazione dell'app Xamarin.tvOS contenga bitcode, aprire l'app Terminal e immettere quanto segue:

```csharp
otool -l /path/to/your/tv.app/tv
```

Nell'output cercare quanto segue:

```csharp
Section
  sectname __bundle
   segname __LLVM
      addr 0x0000000100001000
      size 0x000000000000124f
    offset 4096
     align 2^0 (1)
    reloff 0
    nreloc 0
     flags 0x00000000
 reserved1 0
 reserved2 0
```

`addr` e `size` saranno diversi, ma gli altri campi devono essere identici.

È necessario assicurarsi che tutte le librerie statiche (`.a`) di terze parti in uso siano state compilate in base alle librerie tvOS (non alle librerie iOS) e che includano anche le informazioni di bitcode.

Per le app o le librerie che includono bitcode validi, il `size` sarà maggiore di uno. Esistono situazioni in cui una raccolta può avere il marcatore bitcode, ma non contiene bitcode validi. Esempio:

**Bitcode non valido**

```csharp
 $ otool -arch arm64 libLibrary.a | grep __bitcode -A 3
   sect name __bitcode
   segname __LLVM
      add 0x0000000000000670
      size 0x0000000000000001
```

**Bitcode valido** 

```csharp
$ otool -l -arch arm64 libDownloadableAgent-tvos.a |grep __bitcode -A 3
   sectname __bitcode
   segname __LLVM
      addr 0x000000000001d2d0
      size 0x0000000000045440
```

Si noti la differenza nella `size` tra le due librerie nell'esempio elencato viene eseguito sopra. La libreria deve essere generata da una compilazione di archivio Xcode con bitcode abilitato (impostazione Xcode `ENABLE_BITCODE`) come soluzione a questo problema di dimensioni.

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>Le app che contengono solo la sezione arm64 devono anche avere "arm64" nell'elenco di UIRequiredDeviceCapabilities in info. plist

Quando si invia un'app all'App Store di Apple TV per la pubblicazione, è possibile che venga ricevuto un errore nel formato seguente:

_"Le app che contengono solo la sezione arm64 devono avere anche" arm64 "nell'elenco di UIRequiredDeviceCapabilities in info. plist"._

In tal caso, modificare il file di `Info.plist` e verificare che disponga delle chiavi seguenti:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
  <string>arm64</string>
</array>
```

Ricompilare l'app per il rilascio e inviare nuovamente a iTunes Connect.

### <a name="task-mtouch-execution----failed"></a>Esecuzione dell'attività "MTouch" non riuscita

Se si usa una libreria di terze parti (ad esempio monogame) e la compilazione della versione non è riuscita con una serie di messaggi di errore lunghi che terminano con `Task "MTouch" execution -- FAILED`, provare ad aggiungere `-gcc_flags="-framework OpenAL"` agli **argomenti di tocco aggiuntivi**:

[![](troubleshooting-images/mtouch01.png "Task MTouch execution")](troubleshooting-images/mtouch01.png#lightbox)

È inoltre necessario includere `--bitcode=asmonly` negli **argomenti tocco aggiuntivi**, impostare le opzioni del linker su **collega tutto** ed eseguire una compilazione pulita.

### <a name="itms-90471-error-the-large-icon-is-missing"></a>Errore ITMS-90471. Icona grande mancante

Se viene ricevuto un messaggio nel formato "errore ITMS-90471. L'icona grande è mancante "durante il tentativo di inviare un'app Xamarin.tvOS all'App Store di Apple TV per la versione, controllare quanto segue:

1. Assicurarsi di aver incluso gli asset icone grandi nel file `Assets.car` creato usando la documentazione delle icone dell' [app](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons) .
2. Assicurarsi di aver incluso il file di `Assets.car` dalla documentazione sull' [uso di icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md) nel bundle di applicazioni finale.

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>Bundle non valido: un'app che supporta i controller di gioco deve supportare anche il servizio Apple TV remoto

Oppure 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>Bundle non valido: le app Apple TV con GameController Framework devono includere la chiave GCSupportedGameControllers nel file INFO. plist dell'app

I controller dei giochi possono essere usati per migliorare la giocabilità e fornire un'idea di coinvolgimento in un gioco. Possono anche essere usati per controllare l'interfaccia standard di Apple TV, in modo che l'utente non debba passare tra il controller remoto e il controller.

Se si sta inviando un'app Xamarin.tvOS con il supporto del controller di gioco all'App Store di Apple TV e si sta ricevendo un messaggio di errore nel formato seguente:

_Sono stati individuati uno o più problemi con il recapito recente per "nome app". Il recapito ha avuto esito positivo, ma è possibile risolvere i problemi seguenti durante la distribuzione successiva:_

_Bundle non valido: un'app che supporta i controller di gioco deve supportare anche la modalità remota di Apple TV._

Oppure 

_Bundle non valido: le app Apple TV con GameController Framework devono includere la chiave GCSupportedGameControllers nel file INFO. plist dell'app._

La soluzione consiste nell'aggiungere il supporto per Siri remote (`GCMicroGamepad`) al file di `Info.plist` dell'app. Il profilo di micro Game controller è stato aggiunto da Apple per la destinazione di Siri remote. Includere, ad esempio, le chiavi seguenti:

```xml
<key>GCSupportedGameControllers</key>  
  <array>  
    <dict>  
      <key>ProfileName</key>  
      <string>ExtendedGamepad</string>  
    </dict>  
    <dict>  
      <key>ProfileName</key>  
      <string>MicroGamepad</string>  
    </dict>  
  </array>  
<key>GCSupportsControllerUserInteraction</key>  
<true/>
```

> [!IMPORTANT]
> I controller di gioco Bluetooth sono un acquisto facoltativo che può essere creato dagli utenti finali, l'app non può forzare l'acquisto di un utente. Se l'app supporta i controller di gioco, deve supportare anche Siri remote, in modo che il gioco sia utilizzabile da tutti gli utenti di Apple TV.

Per ulteriori informazioni, vedere la sezione [Working with Game Controllers](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers) della documentazione di [Siri Remote and Bluetooth Controllers](~/ios/tvos/platform/remote-bluetooth.md) .

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>Framework di destinazione incompatibile:. NetPortable, Version = v 4.5, profile = Profile78

Quando si tenta di includere una libreria di classi portabile (PCL) in un progetto Xamarin.tvOS, è possibile che venga ricevuto un messaggio nel formato seguente:

_Framework di destinazione incompatibile:. NetPortable, Version = v 4.5, profile = Profile78_

Per risolvere questo problema, aggiungere un file XML denominato `Xamarin.TVOS.xml` con il contenuto seguente:

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

Nel percorso seguente:

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

Si noti che il numero di profilo nel percorso deve corrispondere al numero di profilo della libreria di classi portabile (PCL).

Con questo file, dovrebbe essere possibile aggiungere correttamente il file PCL al progetto Xamarin.tvOS.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [tvOS](https://developer.apple.com/tvos/)
- [Guide all'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione delle app per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
