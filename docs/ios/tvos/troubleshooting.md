---
title: Risoluzione dei problemi
description: In questo articolo include informazioni conoscono i problemi che potrebbero verificarsi durante l'utilizzo con supporto per tvOS di Xamarin.
ms.topic: article
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/16/2017
ms.openlocfilehash: 278b9e782073a26dc04bac9418613ea4c09db445
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="troubleshooting"></a>Risoluzione dei problemi

_In questo articolo include informazioni conoscono i problemi che potrebbero verificarsi durante l'utilizzo con supporto per tvOS di Xamarin._

<a name="Known-Issues" />

## <a name="known-issues"></a>Problemi noti

La versione corrente di supporto per tvOS di Xamarin presenta i seguenti problemi noti:

- **Framework mono** – Mono 4.3 Cryptography.ProtectedData non riesce a decrittografare i dati da Mono 4.2. Di conseguenza, i pacchetti NuGet avrà esito negativo ripristinare con l'errore `Data unprotection failed` quando viene configurata un'origine di NuGet protetta.
    - **Soluzione alternativa** : In Visual Studio per Mac è necessario aggiungere nuovamente qualsiasi pacchetto NuGet origini che Usa autenticazione di password prima di tentare nuovamente di ripristinare i pacchetti.
- **Visual Studio per Mac con F # Add-in** : errore durante la creazione di un modello di F # Android in Windows. Questo dovrebbe funzionare correttamente su Mac.
- **Xamarin.Mac** : quando si esegue il progetto di modello unificato Xamarin.Mac con il Framework di destinazione impostato su `Unsupported`, la finestra popup `Could not connect to the debugger` possibile che venga visualizzato.
    - **Soluzione alternativa potenziale** : effettuare il Downgrade di versione di framework Mono disponibile nel canale stabile.
- **Xamarin Visual Studio e xamarin** : quando si distribuiscono applicazioni WatchKit in Visual studio, l'errore `The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist` possibile che venga visualizzato.

Per trovare report qualsiasi bug si [Bugzilla](https://bugzilla.xamarin.com/enter_bug.cgi?product=iOS).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Le sezioni seguenti elencano alcuni problemi noti che possono verificarsi quando si utilizza tvOS 9 con Xamarin.tvOS e la soluzione a questi problemi:

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>File eseguibile non valido - il file eseguibile non contiene bitcode

Quando si tenta di inviare una app Xamarin.tvOS in App Store di Apple TV, è possibile che venga visualizzato un messaggio di errore nel modulo _"Eseguibile non valido - il file eseguibile non contiene bitcode"_.

Per risolvere questo problema, effettuare le operazioni seguenti:

1. In Visual Studio per Mac, fare clic sul File di progetto Xamarin.tvOS nel **Esplora** e selezionare **opzioni**.
2. Selezionare **tvOS compilazione** e verificare che trovino nel **versione** configurazione: 

    [ ![](troubleshooting-images/ts01.png "Selezionare le opzioni di compilazione tvOS")](troubleshooting-images/ts01.png)
3. Aggiungere `--bitcode=asmonly` per il **mtouch ulteriori argomenti** campo e fare clic su di **OK** pulsante.
4. Ricompilare l'app nel **versione** configurazione.

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>Verifica che l'App contiene Bitcode tvOS

Per verificare che la compilazione dell'App Xamarin.tvOS contenga Bitcode, aprire l'app Terminal e immettere quanto segue:

```csharp
otool -l /path/to/your/tv.app/tv
```

Nell'output, Cerca quanto segue:

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

È necessario assicurarsi che qualsiasi statico di terze parti (`.a`) si Usa librerie create in base a librerie tvOS (non librerie iOS) e che essi sono incluse anche informazioni bitcode.

Per le applicazioni o librerie che includono bitcode valido di `size` sarà maggiore di uno. Esistono alcune situazioni in cui una libreria può avere il marcatore bitcode, non contenga ancora bitcode valido. Ad esempio:

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

Si noti la differenza in `size` tra le due librerie nell'esempio elencato viene eseguito. La libreria deve essere generata da una compilazione di archiviazione Xcode con bitcode abilitato (impostazione di Xcode `ENABLE_BITCODE`) come soluzione a questo problema di dimensioni.

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>Le applicazioni che contengono solo la porzione arm64 devono avere anche "arm64" nell'elenco di UIRequiredDeviceCapabilities in Info. plist

Quando si invia un'app in App Store di Apple TV per la pubblicazione, si potrebbe ricevere un errore nel formato:

_"Le app che contengono solo la porzione arm64 deve inoltre essere"arm64"nell'elenco di UIRequiredDeviceCapabilities in Info. plist"_

In questo caso, modificare il `Info.plist` file e assicurarsi che sia le chiavi seguenti:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
    <string>arm64</string>
</array>
```

Ricompilare l'app per il rilascio e inviare di nuovo a iTunes Connect.

### <a name="task-mtouch-execution----failed"></a>Esecuzione dell'attività "MTouch", non è riuscita

Se si utilizza una libreria di terze parti 3rd (ad esempio MonoGame) e la compilazione di rilascio non riuscito con una lunga serie di messaggi di errore che terminano con `Task "MTouch" execution -- FAILED`, provare ad aggiungere `-gcc_flags="-framework OpenAL"` per il **tocco ulteriori argomenti**:

[ ![](troubleshooting-images/mtouch01.png "Esecuzione dell'attività MTouch")](troubleshooting-images/mtouch01.png)

È necessario includere anche `--bitcode=asmonly` nel **tocco ulteriori argomenti**, configurato le opzioni del linker per **collegamento tutti** ed eseguire una compilazione pulita.

### <a name="itms-90471-error-the-large-icon-is-missing"></a>ITMS-90471 error. Icona di grandi dimensioni è manca

Se viene visualizzato un messaggio nel formato "ITMS 90471 errore. Icona di grandi dimensioni è manca"durante il tentativo di inviare una app Xamarin.tvOS in App Store di Apple TV per versione, vedere i seguenti:

1. Assicurarsi di avere incluso le risorse di icone grandi nel `Assets.car` file creati usando il [icone App](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons) documentazione.
2. Verificare che incluso il `Assets.car` file dal [utilizzo di immagini e icone](~/ios/tvos/app-fundamentals/icons-images.md) documentazione in bundle dell'applicazione finale.

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>Bundle non valido: un'app che supporta gioco deve supportare anche l'Apple TV remoto

oppure 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>Non valido: bundle di App per Apple TV con il framework GameController deve includere la chiave GCSupportedGameControllers in Info. plist dell'app

Gioco consente di migliorare la modalità di gioco e fornire una stima realistica delle immersion in un gioco. Possono inoltre essere utilizzati per controllare l'interfaccia Apple TV standard, pertanto l'utente non dispone di passare tra il computer remoto e il controller.

Se l'invio di un'app Xamarin.tvOS con il supporto di gioco per Apple TV App store e si riceve un messaggio di errore sotto forma di:


_Sono stati individuati uno o più problemi con la distribuzione di recente per "nome app". La distribuzione ha avuto esito positivo, ma si consiglia di correggere i problemi seguenti nel successivo recapito:_

_Bundle non valido: un'app che supporta gioco deve supportare anche l'Apple TV remoto._

oppure 

_Non valido: bundle di App per Apple TV con il framework GameController deve includere la chiave GCSupportedGameControllers in Info. plist dell'app._

La soluzione consiste nell'aggiungere il supporto per il computer remoto Siri (`GCMicroGamepad`) all'app `Info.plist` file. Il profilo di gioco Micro è stato aggiunto da Apple per Siri remoto di destinazione. Ad esempio, includere le seguenti chiavi:

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
> **Nota:** gioco Bluetooth sono un acquisto facoltativo che potrebbe rendere gli utenti finali, l'app non può forzare l'utente per l'acquisto di uno. Se l'app supporta gioco deve supportare anche Siri remoto in modo che il gioco è utilizzabile da tutti gli utenti di Apple TV.

Per ulteriori informazioni, vedere il nostro [utilizzo di gioco](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers) sezione del nostro [Siri remoto e i controller Bluetooth](~/ios/tvos/platform/remote-bluetooth.md) documentazione.

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>Framework di destinazione non compatibile:. NetPortable, Version = v 4.5, profilo = Profile78

Quando si tenta di includere una libreria di classe portabile (PCL) in un progetto Xamarin.tvOS si potrebbe ricevere un messaggio modo da formare:

_Framework di destinazione non compatibile:. NetPortable, Version = v 4.5, profilo = Profile78_

Per risolvere questo problema, aggiungere un file XML denominato ` Xamarin.TVOS.xml` con il seguente contenuto:

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

Nel seguente percorso:

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

Si noti che il numero di profilo nel percorso deve corrispondere al numero di profilo della libreria di classi Portabile.

Con questo file sul posto, dovrebbe essere possibile aggiungere correttamente il file di libreria di classi Portabile per il progetto Xamarin.tvOS.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [tvOS Guide interfaccia umana](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
