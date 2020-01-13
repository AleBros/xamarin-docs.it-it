---
title: Uso delle impostazioni predefinite dell'utente in Xamarin.iOS
description: Questo articolo illustra l'uso di NSUserDefaults per salvare le impostazioni predefinite in un'estensione o un'app per iOS Novell. Viene descritto NSUserDefaults a un livello elevato e viene illustrato come leggere e scrivere i valori.
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/07/2016
ms.openlocfilehash: 4706b483f8f3a104f54ea2bf451cb4e2fb209486
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73009073"
---
# <a name="working-with-user-defaults-in-xamarinios"></a>Uso delle impostazioni predefinite dell'utente in Xamarin.iOS

_Questo articolo illustra l'uso di NSUserDefault per salvare le impostazioni predefinite in un'estensione o un'app Xamarin.iOS._

La classe `NSUserDefaults` fornisce un modo per le app e le estensioni iOS per interagire a livello di codice con il sistema predefinito a livello di sistema. Usando il sistema predefinito, l'utente può configurare il comportamento o lo stile di un'app per soddisfare le preferenze (in base alla progettazione dell'app). Ad esempio, per presentare i dati nelle misure metrica rispetto a quelle imperiali oppure selezionare un tema dell'interfaccia utente specifico.

Quando viene usato con i gruppi di app, `NSUserDefaults` fornisce anche un modo per comunicare tra le app o le estensioni all'interno di un determinato gruppo.

<a name="About-User-Defaults" />

## <a name="about-user-defaults"></a>Informazioni sulle impostazioni predefinite utente

Come indicato in precedenza, è possibile aggiungere le impostazioni predefinite utente (`NSUserDefaults`) a un'app (o all'estensione) e usare per fornire opzioni configurabili che l'utente finale può modificare per regolare l'aspetto o il funzionamento dell'app in fase di esecuzione.

Quando l'app viene eseguita per la prima volta, `NSUserDefaults` legge le chiavi e i valori del database predefinito dell'utente dell'app e li memorizza nella cache per evitare l'apertura e la lettura del database ogni volta che è necessario un valore. 

> [!IMPORTANT]
> Apple non consiglia più che lo sviluppatore chiami il metodo `Synchronize` per sincronizzare direttamente la cache in memoria con il database. Viene invece chiamato automaticamente a intervalli periodici per tenere sincronizzata la cache in memoria con il database predefinito di un utente.

La classe `NSUserDefaults` contiene diversi metodi pratici per la lettura e la scrittura dei valori di preferenza per i tipi di dati comuni, ad esempio: String, Integer, float, Boolean e URL. Altri tipi di dati possono essere archiviati usando `NSData`, quindi letti o scritti nel database delle impostazioni predefinite dell'utente. Per ulteriori informazioni, vedere la guida per la [programmazione di preferenze e impostazioni](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)di Apple.

<a name="Accessing-the-Shared-NSUserDefaults-Instance" />

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>Accesso all'istanza condivisa di NSUserDefaults 

L'istanza di impostazioni predefinite utente condivise fornisce l'accesso alle impostazioni predefinite dell'utente per l'utente corrente del dispositivo. Se l'oggetto impostazioni predefinite condivise non esiste, viene creato la prima volta che si accede e si inizializza con le informazioni seguenti:

- `NSArgumentDomain` costituito dalle impostazioni predefinite analizzate dall'app corrente.
- Il dominio dell'identificatore del bundle dell'app.
- `NSGlobalDomain` costituito dalle impostazioni predefinite condivise da tutte le app.
- Un dominio separato per ogni lingua preferita dell'utente.
- Un `NSRegistrationDomain` con un set di valori predefiniti temporanei che possono essere modificati dall'app per assicurarsi che le ricerche abbiano sempre esito positivo.

Per accedere all'istanza delle impostazioni predefinite dell'utente condivise, usare il codice seguente:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance" />

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>Accesso a un'istanza di NSUserDefaults del gruppo di app

Come indicato in precedenza, usando i gruppi di app, è possibile usare `NSUserDefaults` per comunicare tra le app (o le estensioni) in un determinato gruppo. Prima di tutto, è necessario assicurarsi che il gruppo di app e gli ID app richiesti siano stati configurati correttamente nella sezione **certificati, identificatori & profili** in [iOS Dev Center](https://developer.apple.com/devcenter/ios/) e siano stati installati nell'ambiente di sviluppo.

Successivamente, i progetti di app e/o di estensione devono avere uno degli ID app validi creati in precedenza e il file di `Entitlements.plist` deve essere incluso nel bundle dell'app con i gruppi di app abilitati e specificati.

A questo punto, è possibile accedere alle impostazioni predefinite utente per il gruppo di app condivise usando il codice seguente:

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

Dove `group.com.xamarin.todaysharing` è il gruppo di app creato in **certificati, identificatori & profili** a cui si vuole accedere. Per ulteriori informazioni, vedere la documentazione relativa alle [funzionalità del gruppo di app](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) .

<a name="Reading-Default-Values" />

## <a name="reading-default-values"></a>Lettura dei valori predefiniti

Dopo aver eseguito l'accesso al database predefinito dell'utente desiderato, è possibile leggere i valori delle impostazioni predefinite usando le coppie chiave/valore e diversi metodi pratici basati sul tipo di dati da leggere:

- `ArrayForKey`: restituisce una matrice di `NSObjects` per il valore di chiave specificato.
- `BoolForKey`: restituisce un valore booleano per la chiave specificata.
- `DataForKey`: restituisce un oggetto `NSData` per la chiave specificata.
- `DictionaryForKey`: restituisce un `NSDictionary` per la chiave specificata.
- `DoubleForKey`: restituisce un valore Double per la chiave specificata.
- `FloatForKey`: restituisce un valore float per la chiave specificata.
- `IntForKey`: restituisce un valore intero per la chiave specificata.
- `StringArrayForKey`: restituisce una matrice di oggetti `String` dal valore di chiave specificato.
- `StringForKey`: restituisce un valore stringa per la chiave specificata.
- `URLForKey`: restituisce un valore `NSUrl` per la chiave specificata.

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

- `SetBool`: scrive il valore booleano specificato nella chiave specificata.
- `SetDouble`: scrive il valore Double specificato nella chiave specificata.
- `SetFloat`: scrive il valore float specificato nella chiave specificata.
- `SetString`: scrive il valore stringa specificato nella chiave specificata.
- `SetURL`: scrive il valore dell'URL (`NSUrl`) specificato nella chiave specificata.

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
> Quando l'app viene eseguita per la prima volta, `NSUserDefaults` legge le chiavi e i valori del database predefinito dell'utente dell'app e li memorizza nella cache per evitare l'apertura e la lettura del database ogni volta che è necessario un valore.

<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato la classe `NSUserDefaults` e come può essere usata per fornire un set di opzioni che l'utente finale può usare per configurare l'app Xamarin.iOS. Inoltre, è stato analizzato usando i gruppi di app per comunicare tra un'estensione e la relativa app padre o tra app in un gruppo.

## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS+tvOS)
- [Guida alla programmazione di preferenze e impostazioni](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)
