---
title: Risoluzione dei problemi delle App tvOS compilate con Xamarin
description: Questo articolo fornisce vari suggerimenti per risolvere i problemi durante lo sviluppo di un'app tvOS compilata con Xamarin. Descrive problemi noti ed errori specifici.
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 124E4953-4DFA-42B0-BCFC-3227508FE4A6
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/16/2017
ms.openlocfilehash: 60b54ffdce5f6bda2324c08bd3159eefeb5bc7f8
ms.sourcegitcommit: 4859da8772dbe920fdd653180450e5ddfb436718
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2018
ms.locfileid: "50234857"
---
# <a name="troubleshooting-tvos-apps-built-with-xamarin"></a>Risoluzione dei problemi delle App tvOS compilate con Xamarin

_Questo articolo illustra conoscenza i problemi che possono verificarsi durante l'utilizzo di supporto di Xamarin per tvOS._

<a name="Known-Issues" />

## <a name="known-issues"></a>Problemi noti

La versione corrente del supporto di Xamarin per tvOS presenta i seguenti problemi noti:

- **Framework mono** – Cryptography.ProtectedData 4.3 di Mono non riesce a decrittografare i dati dalla versione 4.2 di Mono. Di conseguenza, i pacchetti NuGet avrà esito negativo per il ripristino con l'errore `Data unprotection failed` quando è configurata un'origine NuGet protetta.
    - **Soluzione alternativa** : In Visual Studio per Mac è necessario aggiungere nuovamente qualsiasi pacchetto NuGet origini che Usa autenticazione di password prima di ripetere il tentativo di ripristinare i pacchetti.
- **Visual Studio per Mac con F# componente aggiuntivo** : errore durante la creazione di un F# modello di Android in Windows. Questo dovrebbe comunque funzionare correttamente nel Mac.
- **Xamarin. Mac** : quando l'esecuzione del progetto di modello unificata di xamarin. Mac con il Framework di destinazione impostato su `Unsupported`, la finestra popup `Could not connect to the debugger` possibile che venga visualizzato.
    - **Soluzione alternativa potenziale** : effettuare il Downgrade la versione di Mono framework disponibile nel canale stabile.
- **Xamarin Visual Studio e xamarin. IOS** : quando si distribuiscono applicazioni WatchKit in Visual studio, l'errore `The file ‘bin\iPhoneSimulator\Debug\WatchKitApp1WatchKitApp.app\WatchKitApp1WatchKitApp’ does not exist` possibile che venga visualizzato.

Uno qualsiasi dei bug è rapporto è disponibile nel [GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

## <a name="troubleshooting"></a>Risoluzione dei problemi

Le sezioni seguenti elencano alcuni problemi noti che possono verificarsi quando si usa tvOS 9 con xamarin. tvos e la soluzione per risolvere tali problemi:

### <a name="invalid-executable---the-executable-does-not-contain-bitcode"></a>File eseguibile non è valido - il file eseguibile non contengono bitcode

Quando si tenta di inviare un'app xamarin. tvos la Store di App Apple TV, si potrebbe ottenere un messaggio di errore nel modulo _"Eseguibile non è valido - il file eseguibile non contengono bitcode"_.

Per risolvere questo problema, effettuare le operazioni seguenti:

1. In Visual Studio per Mac, fare doppio clic sul File di progetto xamarin. tvos nel **Esplora soluzioni** e selezionare **opzioni**.
2. Selezionare **compilazione tvOS** e assicurarsi di trovarsi nel **rilascio** configurazione: 

    [![](troubleshooting-images/ts01.png "Selezionare le opzioni di compilazione tvOS")](troubleshooting-images/ts01.png#lightbox)
3. Aggiungere `--bitcode=asmonly` per il **argomenti aggiuntivi di mtouch** campo e fare clic sui **OK** pulsante.
4. Ricompilare l'app nel **rilascio** configurazione.

### <a name="verifying-that-your-tvos-app-contains-bitcode"></a>Verifica per determinare se l'App contiene Bitcode tvOS

Per verificare che la compilazione dell'App Xamarin.tvOS contenga Bitcode, aprire l'app Terminal e immettere le informazioni seguenti:

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

`addr` e `size` sarà diverso, ma gli altri campi devono essere identici.

È necessario assicurarsi che terze parti statiche (`.a`) le librerie in uso sono state compilate a librerie tvOS (non le librerie di iOS) e include anche informazioni bitcode.

Per le app o le raccolte che includono bitcode valido il `size` sarà maggiore di uno. Esistono alcune situazioni in cui possa avere il marcatore bitcode una libreria, ma non contengono bitcode valido. Ad esempio:

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

Si noti la differenza in `size` tra le due librerie nell'esempio elencato viene eseguito in precedenza. La libreria deve essere generata da una compilazione di archiviazione di Xcode con bitcode abilitato (impostazione di Xcode `ENABLE_BITCODE`) come soluzione al problema delle dimensioni.

### <a name="apps-that-only-contain-the-arm64-slice-must-also-have-arm64-in-the-list-of-uirequireddevicecapabilities-in-infoplist"></a>Le app che contengono solo la sezione arm64 deve anche essere "arm64" nell'elenco di UIRequiredDeviceCapabilities in Info. plist

Durante l'invio di un'app di Store di App TV Apple per la pubblicazione, si potrebbe essere visualizzato un errore nel formato:

_"Le app che contengono solo la sezione arm64 deve anche essere"arm64"nell'elenco di UIRequiredDeviceCapabilities in Info. plist"_

In questo caso, modificare il `Info.plist` file e verificare che abbia le chiavi seguenti:

```xml
<key>UIRequiredDeviceCapabilities</key>
<array>
    <string>arm64</string>
</array>
```

Ricompilare l'app per la versione e inviare di nuovo a iTunes Connect.

### <a name="task-mtouch-execution----failed"></a>Esecuzione di attività "MTouch": non è riuscita

Se si usa una libreria di terze parti 3rd (ad esempio MonoGame) e la compilazione di rilascio non è riuscita con una lunga serie di messaggi di errore che termina con `Task "MTouch" execution -- FAILED`, provare ad aggiungere `-gcc_flags="-framework OpenAL"` per il **touch ulteriori argomenti**:

[![](troubleshooting-images/mtouch01.png "Esecuzione dell'attività di MTouch")](troubleshooting-images/mtouch01.png#lightbox)

È necessario includere anche `--bitcode=asmonly` nella **gli argomenti aggiuntivi di tocco**, sono le opzioni del linker impostata su **collega tutto** ed eseguire una compilazione pulita.

### <a name="itms-90471-error-the-large-icon-is-missing"></a>Errore 90471 ITMS. Icona di grandi dimensioni è manca

Se viene visualizzato un messaggio nel formato "ITMS 90471 error. Icona di grandi dimensioni manca"durante il tentativo di inviare un'app xamarin. tvos la Store di App TV Apple per la versione, verificare quanto segue:

1. Assicurarsi di aver inserito gli asset icone grandi nel `Assets.car` file che è stato creato mediante il [icone dell'App](~/ios/tvos/app-fundamentals/icons-images.md#App-Icons) documentazione.
2. Verificare che incluso il `Assets.car` del file dal [utilizzo di icone e immagini](~/ios/tvos/app-fundamentals/icons-images.md) documentazione nel bundle dell'applicazione finale.

### <a name="invalid-bundle--an-app-that-supports-game-controllers-must-also-support-the-apple-tv-remote"></a>Bundle non valido: un'app che supporta periferiche di gioco deve supportare anche il remoto Apple TV

oppure 

### <a name="invalid-bundle--apple-tv-apps-with-the-gamecontroller-framework-must-include-the-gcsupportedgamecontrollers-key-in-the-apps-infoplist"></a>Bundle non valido: le app Apple TV con framework GameController deve includere la chiave GCSupportedGameControllers in Info. plist dell'app

Periferiche di gioco sono utilizzabile per migliorare la modalità di gioco e fornire un senso di full immersion in un gioco. Possono inoltre essere utilizzati per controllare l'interfaccia Apple TV standard in modo che l'utente non dispone di passare tra il computer remoto e il controller.

Se si invia un'app xamarin. tvos con supporto per il Controller di gioco per Apple TV App store e si riceve un messaggio di errore sotto forma di:


_È stato rilevato uno o più problemi con il recapito di recente per "nome dell'app". Il recapito è riuscito, ma è possibile correggere i problemi seguenti nella propria distribuzione successiva:_

_Bundle non valido: un'app che supporta periferiche di gioco deve supportare anche il remoto Apple TV._

oppure 

_Bundle non valido: le app Apple TV con framework GameController deve includere la chiave GCSupportedGameControllers in Info. plist dell'app._

La soluzione consiste nell'aggiungere il supporto per il remoti per Siri (`GCMicroGamepad`) all'app `Info.plist` file. Il profilo del Controller di gioco Micro è stato aggiunto da Apple per Siri remoto di destinazione. Ad esempio, includere le chiavi seguenti:

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
> Bluetooth gioco sono un acquisto facoltativo che potrebbero rendere gli utenti finali, l'app non può forzare l'utente per l'acquisto di uno. Se l'app supporta periferiche di gioco deve anche supportare il remoti per Siri in modo che il gioco è utilizzabile da tutti gli utenti di Apple TV.

Per altre informazioni, vedere la [uso di periferiche di gioco](~/ios/tvos/platform/remote-bluetooth.md#Working-with-Game-Controllers) sezione del nostro [controller Bluetooth e remoti per Siri](~/ios/tvos/platform/remote-bluetooth.md) documentazione.

### <a name="incompatible-target-framework-netportable-versionv45-profileprofile78"></a>Framework di destinazione incompatibile:. NetPortable, versione = v4.5, profilo = Profile78

Quando si tenta di includere una libreria di classi portabile (PCL) in un progetto xamarin. tvos si potrebbe essere visualizzato un messaggio in modo da formare:

_Framework di destinazione incompatibile:. NetPortable, versione = v4.5, profilo = Profile78_

Per risolvere questo problema, aggiungere un file XML denominato ` Xamarin.TVOS.xml` con il contenuto seguente:

```xml
<Framework Identifier="Xamarin.TVOS" MinimumVersion="1.0" Profile="*" DisplayName="Xamarin.TVOS"/>
```

Nel seguente percorso:

```csharp
/Library/Frameworks/Mono.framework/Versions/Current/lib/mono/xbuild-frameworks/.NETPortable/v4.5/Profile/Profile259/SupportedFrameworks/

```

Si noti che il numero di profilo del percorso deve corrispondere al numero di profili della libreria di classi Portabile.

Con questo file sul posto, dovrebbe essere possibile aggiungere correttamente il file di libreria di classi Portabile per il progetto xamarin. tvos.



## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [tvOS](https://developer.apple.com/tvos/)
- [le guide dell'interfaccia umana tvOS](https://developer.apple.com/tvos/human-interface-guidelines/)
- [Guida alla programmazione di App per tvOS](https://developer.apple.com/library/prerelease/tvos/documentation/General/Conceptual/AppleTV_PG/)
