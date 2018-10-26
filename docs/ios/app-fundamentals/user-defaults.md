---
title: Utilizzo di impostazioni predefinite dell'utente in xamarin. IOS
description: Questo articolo descrive l'uso con NSUserDefaults per salvare le impostazioni predefinite in un'estensione o app per Xamarin iOS. Descrive NSUserDefaults a livello generale e viene illustrato come leggere e scrivere i valori.
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 06/07/2016
ms.openlocfilehash: 688db534d6c99a8fadb7535f0532f9c1e9564707
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50120892"
---
# <a name="working-with-user-defaults-in-xamarinios"></a>Utilizzo di impostazioni predefinite dell'utente in xamarin. IOS

_Questo articolo descrive l'uso con NSUserDefault per salvare le impostazioni predefinite in un'App di Xamarin.iOS o estensione._


Il `NSUserDefaults` classe fornisce un modo per iOS App ed estensioni per interagire a livello di codice con il sistema per impostazione predefinita a livello di sistema. Tramite il sistema per impostazione predefinita, l'utente può configurare il comportamento di un'app o applicazione di stili per soddisfare le proprie preferenze (in base alla struttura dell'app). Ad esempio, per presentare i dati in Visual Studio metrica misurazioni imperiale o selezionare un tema dell'interfaccia utente specificato.

Quando usato con i gruppi di App, `NSUserDefaults` fornisce inoltre un modo per la comunicazione tra App (o le estensioni) all'interno di un dato gruppo.

<a name="About-User-Defaults" />

## <a name="about-user-defaults"></a>Informazioni sulle impostazioni predefinite utente

Come indicato in precedenza, per impostazione predefinita utente (`NSUserDefaults`) può essere aggiunto a un'App (o estensione) e utilizzato per fornire opzioni configurabili che gli utenti finali possono modificare per regolare l'aspetto o il funzionamento dell'app in fase di esecuzione.

Quando l'app viene eseguita prima di tutto, `NSUserDefaults` legge le chiavi e valori dal Database per impostazione predefinita utente dell'app e li memorizza nella cache in memoria per evitare di apertura e la lettura del database ogni volta che è richiesto un valore. 

> [!IMPORTANT]
> Apple non consiglia non è più la chiamata per gli sviluppatori di `Synchronize` metodo per la sincronizzazione direttamente la cache in memoria con il database. Al contrario, verrà automaticamente chiamato a intervalli regolari per sincronizzare la cache in memoria con un database dell'utente impostazioni predefinite.

Il `NSUserDefaults` classe contiene molti metodi pratici per la lettura e scrittura i valori delle preferenze per tipi di dati comuni, ad esempio: stringa, integer, float, boolean e gli URL. Altri tipi di dati possono essere archiviati usando `NSData`, quindi lette o scritte nel database utente per impostazione predefinita. Per altre informazioni, vedere di Apple [Guida alla programmazione di impostazioni e preferenze](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i).

<a name="Accessing-the-Shared-NSUserDefaults-Instance" />

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>Che accedono all'istanza NSUserDefaults condivisa 

L'istanza di valore predefinito è condiviso utente fornisce l'accesso per impostazione predefinita è l'utente per l'utente corrente del dispositivo. Se l'oggetto condiviso per impostazione predefinita non esiste, viene creato la prima volta è accessibile e inizializzato con le informazioni seguenti:

- Un `NSArgumentDomain` costituita da impostazioni predefinite analizzate dall'app corrente.
- Dominio di identificatore del Bundle dell'app.
- Un `NSGlobalDomain` costituita da impostazioni predefinite condivise da tutte le app.
- Un dominio separato per ognuna delle lingue preferite dell'utente.
- Un `NSRegistrationDomain` con un set di impostazioni predefinite temporanei che possono essere modificate dall'app per verificare che le ricerche sono sempre esito positivo.

Per accedere all'istanza del valore predefinito è condiviso utente, usare il codice seguente:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance" />

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>L'accesso a un'istanza di NSUserDefaults gruppo di App

Come indicato in precedenza, usando i gruppi di App, `NSUserDefaults` può essere utilizzato per stabilire la comunicazione tra App (o le estensioni) all'interno di un dato gruppo. In primo luogo, sarà necessario assicurarsi che il gruppo di App e gli ID dell'App necessarie sono stati configurati correttamente nel **certificati, identificatori e profili** sezione [iOS Dev Center](https://developer.apple.com/devcenter/ios/) e sono stati installati Nell'ambiente di sviluppo.

Successivamente, i progetti di App e/o l'estensione è necessario avere uno degli ID App valido creato in precedenza e il `Entitlements.plist` file deve essere incluso nel Bundle dell'App con i gruppi di App abilitata ed è specificato.

Con tutto questo posto, condivise App gruppo utente predefinite sono accessibili tramite il codice seguente:

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

In cui `group.com.xamarin.todaysharing` viene creato il gruppo di App in **certificati, identificatori e profili** che si desidera accedere. Per altre informazioni, vedere la [le funzionalità di gruppi di App](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentazione.

<a name="Reading-Default-Values" />

## <a name="reading-default-values"></a>Leggere i valori predefiniti

Dopo che è stato eseguito l'accesso al Database predefinito utente desiderato, è possibile leggere i valori da quello predefinito usando molti metodi pratici in base al tipo di dati letti e coppie chiave/valore:

- `ArrayForKey` : Restituisce una matrice di `NSObjects` per il valore di chiave specificato.
- `BoolForKey` : Restituisce un valore booleano per la chiave specificata.
- `DataForKey` : Restituisce un `NSData` oggetto per la chiave specificata.
- `DictionaryForKey` : Restituisce un `NSDictionary` per la chiave specificata.
- `DoubleForKey` : Restituisce un valore double per la chiave specificata.
- `FloatForKey` : Restituisce un valore float per la chiave specificata.
- `IntForKey` : Restituisce un valore intero per la chiave specificata.
- `StringArrayForKey` : Restituisce una matrice di `String` oggetti dal valore di chiave specificato.
- `StringForKey` : Restituisce un valore stringa per la chiave specificata.
- `URLForKey` : Restituisce un `NSUrl` valore per la chiave specificata.

Ad esempio, il codice seguente legge un valore booleano da valore predefinito è l'utente:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Get value
var useHeader = plist.BoolForKey("UseHeader");

```

<a name="Writing-Default-Values" />

## <a name="writing-default-values"></a>La scrittura di valori predefiniti

Esattamente come la lettura dei valori precedenti, dopo che è stato eseguito l'accesso utente predefinito Database desiderato, è possibile scrivere i valori per le impostazioni predefinite usando molti metodi pratici in base al tipo di dati da scrivere e coppie chiave/valore:

- `SetBool` -Scrive il valore booleano specificato per la chiave specificata.
- `SetDouble` -Scrive il valore double specificato per la chiave specificata.
- `SetFloat` -Scrive il valore float specificato per la chiave specificata.
- `SetString` -Scrive il valore di stringa specificata per la chiave specificata.
- `SetURL` -Scrive l'URL specificato (`NSUrl`) valore per la chiave specificata.

Il codice seguente sarebbe ad esempio, scrivere un valore booleano per impostazione predefinita è l'utente:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Save value
plist.SetBool(useHeader, "UseHeader");
...

```

> [!IMPORTANT]
> Quando l'App viene eseguita prima di tutto, `NSUserDefaults` legge le chiavi e valori dal Database per impostazione predefinita utente dell'app e li memorizza nella cache in memoria per evitare di apertura e la lettura del database ogni volta che è richiesto un valore.



<a name="Summary" />

## <a name="summary"></a>Riepilogo

Questo articolo ha illustrato le `NSUserDefaults` classe e come può essere usato per fornire un set di opzioni che l'utente finale può usare per configurare l'App Xamarin.iOS. Inoltre, sia nascosto tramite i gruppi di App per la comunicazione tra un'estensione e le App padre o tra App in un gruppo.


## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Impostazione delle preferenze e Guida per programmatori](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)
