---
title: Utilizzo di impostazioni predefinite dell'utente
description: In questo articolo viene descritto l'utilizzo con NSUserDefault per salvare le impostazioni predefinite in un iOS Xamarin App o un'estensione.
ms.topic: article
ms.prod: xamarin
ms.assetid: DAE7FFC4-B8C9-4D9E-886A-9B2388452EEB
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/07/2016
ms.openlocfilehash: 73f3beb87fffcb37ef3e36d54f634c3bc62da538
ms.sourcegitcommit: 73bd0c7e5f237f0a1be70a6c1384309bb26609d5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2018
---
# <a name="working-with-user-defaults"></a>Utilizzo di impostazioni predefinite dell'utente

_In questo articolo viene descritto l'utilizzo con NSUserDefault per salvare le impostazioni predefinite in un'App Xamarin.iOS o un'estensione._


Il `NSUserDefaults` classe fornisce un modo per iOS App e le estensioni per l'interazione a livello di codice con il sistema di impostazioni predefinite a livello di sistema. Tramite il sistema per impostazione predefinita, l'utente può configurare il comportamento di un'app o gli stili per soddisfare le proprie preferenze (in base alla struttura dell'app). Ad esempio, per presentare i dati in Visual Studio metrica misurazioni imperiale o selezionare un tema dell'interfaccia utente specificato.

Se usato con i gruppi di App, `NSUserDefaults` fornisce inoltre un modo per le comunicazioni tra le App (o le estensioni) all'interno di un determinato gruppo.

<a name="About-User-Defaults" />

## <a name="about-user-defaults"></a>Informazioni sulle impostazioni predefinite di utente

Come descritto in precedenza, impostazioni predefinite dell'utente (`NSUserDefaults`) può essere aggiunto a un App (o estensione) e utilizzato per fornire opzioni configurabili che è possibile modificare l'utente finale per regolare l'aspetto o il funzionamento dell'app in fase di esecuzione.

Quando si esegue prima l'app, `NSUserDefaults` legge le chiavi e valori da Database di impostazioni predefinite dell'applicazione utente e li memorizza nella cache in memoria per evitare di apertura e la lettura del database ogni volta che è necessario specificare un valore. 

> [!IMPORTANT]
> Apple non consiglia non è più la chiamata di sviluppatore di `Synchronize` metodo per la sincronizzazione direttamente la cache in memoria con il database. Al contrario, verrà automaticamente chiamato a intervalli regolari per mantenere sincronizzati con un database dell'utente predefinite nella cache in memoria.

La `NSUserDefaults` classe contiene vari metodi pratici per leggere e scrivere i valori delle preferenze per i tipi di dati comuni, ad esempio: string, integer, float, boolean e URL. Altri tipi di dati possono essere archiviati usando `NSData`, quindi leggere o scrivere al Database per impostazione predefinita. Per ulteriori informazioni, vedere Apple [preferenze e impostazioni di Guida per programmatori](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i).

<a name="Accessing-the-Shared-NSUserDefaults-Instance" />

## <a name="accessing-the-shared-nsuserdefaults-instance"></a>Che accedono all'istanza condivisa NSUserDefaults 

L'istanza di impostazioni predefinite condiviso utente fornisce l'accesso per le impostazioni predefinite dell'utente per l'utente corrente del dispositivo. Se non esiste l'oggetto condivisi per impostazione predefinita, viene creato la prima volta è accessibile e inizializzato con le informazioni seguenti:

- Un `NSArgumentDomain` costituito le impostazioni predefinite analizzate da app corrente.
- Dominio identificatore Bundle dell'app.
- Un `NSGlobalDomain` costituito le impostazioni predefinite condivise da tutte le app.
- Un dominio distinto per ognuna delle lingue preferite dell'utente.
- Un `NSRegistrationDomain` con un set di impostazioni predefinite temporanei che possono essere modificate dall'app per verificare le ricerche sono sempre esito positivo.

Per accedere all'istanza del valore predefinito è condiviso utente, utilizzare il codice seguente:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
```

<a name="Accessing-an-App-Group-NSUserDefaults-Instance" />

## <a name="accessing-an-app-group-nsuserdefaults-instance"></a>Accesso a un'istanza di gruppo App NSUserDefaults

Come descritto in precedenza, tramite i gruppi di App, `NSUserDefaults` può essere utilizzato per la comunicazione tra le App (o estensioni) all'interno di un gruppo specifico. In primo luogo, è necessario assicurarsi che il gruppo dell'applicazione e gli ID dell'App necessarie sono stati configurati correttamente nel **certificati, profili & identificatori** sezione [iOS Dev Center](https://developer.apple.com/devcenter/ios/) e sono stati installati Nell'ambiente di sviluppo.

Successivamente, i progetti di App e/o estensione necessario disporre di uno degli ID App valido creato in precedenza e il `Entitlements.plist` file deve essere incluso nel Bundle di App con i gruppi di App abilitata ed è specificato.

Con questo all posto, condiviso App gruppo utente impostazioni predefinite del è possibile accedere tramite il codice seguente:

```csharp
// Get App Group User Defaults
var plist = new NSUserDefaults ("group.com.xamarin.todaysharing", NSUserDefaultsType.SuiteName);
```

Dove `group.com.xamarin.todaysharing` viene creato il gruppo di App in **certificati, profili & identificatori** che si desidera accedere. Per ulteriori informazioni, vedere il [le funzionalità delle App gruppo](~/ios/deploy-test/provisioning/capabilities/app-groups-capabilities.md) documentazione.

<a name="Reading-Default-Values" />

## <a name="reading-default-values"></a>Lettura dei valori predefiniti

Dopo che è stato eseguito l'accesso al Database predefinito utente desiderato, è possibile leggere i valori dalle impostazioni predefinite utilizzando diversi metodi pratici, in base al tipo di dati in lettura e coppie chiave/valore:

- `ArrayForKey` : Restituisce una matrice di `NSObjects` per il valore di chiave specificato.
- `BoolForKey` : Restituisce un valore booleano per la chiave specificata.
- `DataForKey` : Restituisce un `NSData` oggetto per la chiave specificata.
- `DictionaryForKey` : Restituisce un `NSDictionary` la chiave specificata.
- `DoubleForKey` : Restituisce un valore double la chiave specificata.
- `FloatForKey` : Restituisce un valore float per la chiave specificata.
- `IntForKey` : Restituisce un valore intero per la chiave specificata.
- `StringArrayForKey` : Restituisce una matrice di `String` oggetti dal valore di chiave specificato.
- `StringForKey` : Restituisce un valore stringa per la chiave specificata.
- `URLForKey` : Restituisce un `NSUrl` valore per la chiave specificata.

Ad esempio, il codice seguente sarebbe leggere un valore booleano dalle impostazioni predefinite dell'utente:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Get value
var useHeader = plist.BoolForKey("UseHeader");

```

<a name="Writing-Default-Values" />

## <a name="writing-default-values"></a>Scrittura di valori predefiniti

Come leggere i valori superiori, dopo che è stato eseguito l'accesso utente predefinito Database desiderato, è possibile scrivere i valori per le impostazioni predefinite utilizzando diversi metodi pratici, in base al tipo di dati e coppie chiave/valore:

- `SetBool` -Scrive il valore booleano specificato per la chiave specificata.
- `SetDouble` -Scrive il valore double specificato per la chiave specificata.
- `SetFloat` -Scrive il valore float specificato per la chiave specificata.
- `SetString` -Scrive il valore di stringa specificata per la chiave specificata.
- `SetURL` -Scrive l'URL specificato (`NSUrl`) valore per la chiave specificata.

Ad esempio, il codice seguente è necessario scrivere un valore booleano per le impostazioni predefinite dell'utente:

```csharp
// Get Shared User Defaults
var plist = NSUserDefaults.StandardUserDefaults;
...

// Save value
plist.SetBool(useHeader, "UseHeader");
...

```

> [!IMPORTANT]
> Quando l'App viene eseguita prima di tutto, `NSUserDefaults` legge le chiavi e valori da impostazioni predefinite Database degli utenti dell'app e li memorizza nella cache in memoria per evitare di apertura e la lettura del database ogni volta che è necessario specificare un valore.



<a name="Summary" />

## <a name="summary"></a>Riepilogo

In questo articolo è illustrati i `NSUserDefaults` classe e come può essere usato per fornire un set di opzioni che l'utente finale può utilizzare per configurare l'App Xamarin.iOS. Inoltre, che trattato usano gruppi di App per la comunicazione tra un'estensione e il relativo elemento padre di App o tra le App in un gruppo.


## <a name="related-links"></a>Collegamenti correlati

- [Esempi di tvOS](https://developer.xamarin.com/samples/tvos/all/)
- [Le preferenze e impostazioni di Guida per programmatori](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html#//apple_ref/doc/uid/10000059i)
- [NSUserDefaults](https://developer.apple.com/library/mac/documentation/Cocoa/Reference/Foundation/Classes/NSUserDefaults_Class/#//apple_ref/doc/constant_group/NSUserDefaults_Domains)
