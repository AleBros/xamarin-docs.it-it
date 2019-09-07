---
title: Uso delle impostazioni predefinite dell'utente in Novell. iOS
description: Questo articolo illustra l'uso di NSUserDefaults per salvare le impostazioni predefinite in un'estensione o un'app per iOS Novell. Viene descritto NSUserDefaults a un livello elevato e viene illustrato come leggere e scrivere i valori.
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 06/07/2016
ms.openlocfilehash: 4db5718394c3835861f3dfa8b99a4bac70e26981
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70767009"
---
# <a name="working-with-user-defaults-in-xamarinios"></a>Uso delle impostazioni predefinite dell'utente in Novell. iOS

_Questo articolo illustra l'uso di NSUserDefault per salvare le impostazioni predefinite in un'estensione o un'app Novell. iOS._

La `NSUserDefaults` classe fornisce un modo per le app e le estensioni iOS per interagire a livello di codice con il sistema predefinito a livello di sistema. Usando il sistema predefinito, l'utente può configurare il comportamento o lo stile di un'app per soddisfare le preferenze (in base alla progettazione dell'app). Ad esempio, per presentare i dati nelle misure metrica rispetto a quelle imperiali oppure selezionare un tema dell'interfaccia utente specifico.

Quando viene usato con i gruppi `NSUserDefaults` di app, fornisce anche un modo per comunicare tra le app (o le estensioni) in un determinato gruppo.

<a name="About-User-Defaults" />

## <a name="about-user-defaults"></a>Informazioni sulle impostazioni predefinite utente

Come indicato in precedenza, i valori predefiniti`NSUserDefaults`utente () possono essere aggiunti a un'app (o all'estensione) e usati per fornire opzioni configurabili che l'utente finale può modificare per regolare l'aspetto o il funzionamento dell'app in fase di esecuzione.

Quando l'app viene eseguita per la `NSUserDefaults` prima volta, legge le chiavi e i valori del database predefinito dell'utente dell'app e li memorizza nella cache per evitare l'apertura e la lettura del database ogni volta che è necessario un valore. 

> [!IMPORTANT]
> Apple non consiglia più che lo sviluppatore chiami il `Synchronize` metodo per sincronizzare la cache in memoria direttamente con il database. Viene invece chiamato automaticamente a intervalli periodici per tenere sincronizzata la cache in memoria con il database predefinito di un utente.

La `NSUserDefaults` classe contiene diversi metodi pratici per la lettura e la scrittura dei valori di preferenza per i tipi di dati comuni, ad esempio: String, Integer, float, Boolean e URL. Altri tipi di dati possono essere archiviati usando `NSData`, quindi leggere o scrivere nel database delle impostazioni predefinite dell'utente. Per ulteriori informazioni, vedere la guida per la [programmazione di preferenze e impostazioni](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)di Apple.

<a name="Accessing-the-Shared-NSUserDefaults-Instance" />

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>Accesso all'istanza condivisa di NSUserDefaults 

L'istanza di impostazioni predefinite utente condivise fornisce l'accesso alle impostazioni predefinite dell'utente per l'utente corrente del dispositivo. Se l'oggetto impostazioni predefinite condivise non esiste, viene creato la prima volta che si accede e si inizializza con le informazioni seguenti:

- Oggetto `NSArgumentDomain` costituito dalle impostazioni predefinite analizzate dall'app corrente.
- Il dominio dell'identificatore del bundle dell'app.
- Oggetto `NSGlobalDomain` costituito dalle impostazioni predefinite condivise da tutte le app.
- Un dominio separato per ogni lingua preferita dell'utente.
- `NSRegistrationDomain` Con un set di valori predefiniti temporanei che possono essere modificati dall'app per assicurarsi che le ricerche abbiano sempre esito positivo.

Per accedere all'istanza delle impostazioni predefinite dell'utente condivise, usare il codice seguente:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance" />

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>Accesso a un'istanza di NSUserDefaults del gruppo di app

Come indicato in precedenza, l'uso di gruppi `NSUserDefaults` di app può essere usato per la comunicazione tra app (o estensioni) in un determinato gruppo. Prima di tutto, è necessario assicurarsi che il gruppo di app e gli ID app richiesti siano stati configurati correttamente nella sezione **certificati, identificatori & profili** in [iOS Dev Center](https://developer.apple.com/devcenter/ios/) e siano stati installati nell'ambiente di sviluppo.

Successivamente, i progetti di app e/o di estensione devono avere uno degli ID app validi creati in precedenza e il `Entitlements.plist` file deve essere incluso nel bundle dell'app con i gruppi di app abilitati e specificati.

A questo punto, è possibile accedere alle impostazioni predefinite utente per il gruppo di app condivise usando il codice seguente:

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

Dove `group.com.xamarin.todaysharing` è il gruppo di app creato in **certificati, identificatori & profili** a cui si vuole accedere. Per altre informazioni, vedere la [le funzionalità di gruppi di App](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentazione.

<a name="Reading-Default-Values" />

## <a name="reading-default-values"></a>Lettura dei valori predefiniti

Dopo aver eseguito l'accesso al database predefinito dell'utente desiderato, è possibile leggere i valori delle impostazioni predefinite usando le coppie chiave/valore e diversi metodi pratici basati sul tipo di dati da leggere:

- `ArrayForKey`: Restituisce una matrice di `NSObjects` per il valore di chiave specificato.
- `BoolForKey`: Restituisce un valore booleano per la chiave specificata.
- `DataForKey`: Restituisce un `NSData` oggetto per la chiave specificata.
- `DictionaryForKey`: Restituisce un `NSDictionary` oggetto per la chiave specificata.
- `DoubleForKey`: Restituisce un valore Double per la chiave specificata.
- `FloatForKey`: Restituisce un valore float per la chiave specificata.
- `IntForKey`: Restituisce un valore intero per la chiave specificata.
- `StringArrayForKey`: Restituisce una matrice di `String` oggetti dal valore di chiave specificato.
- `StringForKey`: Restituisce un valore stringa per la chiave specificata.
- `URLForKey`: Restituisce un `NSUrl` valore per la chiave specificata.

Il codice seguente, ad esempio, legge un valore booleano dalle impostazioni predefinite dell'utente:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Get value
var useHeader = plist.BoolForKey("UseHeader");

```

<a name="Writing-Default-Values" />

## <a name="writing-default-values"></a>Scrittura di valori predefiniti

Proprio come per la lettura dei valori precedenti, dopo aver eseguito l'accesso al database predefinito dell'utente desiderato, è possibile scrivere i valori predefiniti usando le coppie chiave/valore e diversi metodi pratici basati sul tipo di dati scritti:

- `SetBool`: Scrive il valore booleano specificato nella chiave specificata.
- `SetDouble`: Scrive il valore Double specificato nella chiave specificata.
- `SetFloat`: Scrive il valore float specificato nella chiave specificata.
- `SetString`: Scrive il valore stringa specificato nella chiave specificata.
- `SetURL`: Scrive il valore dell'URL`NSUrl`() specificato nella chiave specificata.

Il codice seguente, ad esempio, scriverebbe un valore booleano per le impostazioni predefinite dell'utente:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Save value
plist.SetBool(useHeader, "UseHeader");
...

```

> [!IMPORTANT]
> Quando l'app viene eseguita per la `NSUserDefaults` prima volta, legge le chiavi e i valori del database predefinito dell'utente dell'app e li memorizza nella cache per evitare l'apertura e la lettura del database ogni volta che è necessario un valore.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la `NSUserDefaults` classe e come può essere usata per fornire un set di opzioni che l'utente finale può usare per configurare l'app Novell. iOS. Inoltre, è stato analizzato usando i gruppi di app per comunicare tra un'estensione e la relativa app padre o tra app in un gruppo.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Guida alla programmazione di preferenze e impostazioni](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)
