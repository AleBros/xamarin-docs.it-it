---
title: Nozioni fondamentali sull'applicazione Xamarin.Mac
description: Questo documento contiene collegamenti a guide che descrivono i diversi concetti necessari per lo sviluppo di applicazioni Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 12/17/2015
ms.openlocfilehash: 2603360162ee9918e83b9f5c74b8086f71d02df8
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73030094"
---
# <a name="xamarinmac-application-fundamentals"></a>Nozioni fondamentali sull'applicazione Xamarin.Mac

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[Modelli e idiomi comuni](~/mac/app-fundamentals/patterns.md)

In tutte le API Apple esposte tramite C#, determinati idiomi e modelli vengono riavviati più volte. Se si ha esperienza con la programmazione con Xamarin.iOS, questi possono avere un aspetto familiare. La documentazione si riferisce spesso a questi schemi e idiomi ripetutamente, quindi avere una conoscenza approfondita di questi concetti ti aiuterà a comprendere la documentazione che trovi.

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[Informazioni sulle API Mac](~/mac/app-fundamentals/mac-apis.md)

Per la maggior parte del tempo di sviluppo con Xamarin.Mac, è possibile pensare, leggere e scrivere C# senza preoccuparsi delle API Objective-C sottostanti. Tuttavia, a volte è necessario leggere la documentazione dell'API da Apple, tradurre una risposta da Stack Overflow a una soluzione per il problema oppure confrontarla con un esempio esistente.

## <a name="console-appsmacapp-fundamentalsconsolemd"></a>[App console](~/mac/app-fundamentals/console.md)

È anche possibile creare app console "inutilizzate" che accedono alle API macOS native usando Xamarin.Mac.

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[Uso dei file con estensione XIB](~/mac/app-fundamentals/xib.md)

Questo articolo illustra l'uso dei file con estensione XIB creati nella Interface Builder di Xcode per creare e gestire interfacce utente per un'applicazione Xamarin.Mac.

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[. Storyboard/. xib meno progettazione dell'interfaccia utente](~/mac/app-fundamentals/xibless-ui.md)

Questo articolo illustra la creazione di un'interfaccia utente dell'applicazione Xamarin.Mac C# direttamente dal codice senza usare Interface Builder di Xcode con i file. Storyboard o. xib.

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[Uso delle immagini](~/mac/app-fundamentals/image.md)

Questo articolo illustra l'uso di immagini e icone in un'applicazione Xamarin.Mac. Viene illustrata la creazione e la gestione delle immagini necessarie per creare l'icona dell'applicazione e l' C# uso di immagini sia nel codice che nel Interface Builder di Xcode.

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[Data Binding e codifica chiave-valore](~/mac/app-fundamentals/databinding.md)

Questo articolo illustra l'uso della codifica chiave-valore e l'osservazione chiave-valore per consentire data binding agli elementi dell'interfaccia utente nella Interface Builder di Xcode. Utilizzando questa tecnica, si riduce notevolmente la quantità di C# codice che deve essere scritta per l'applicazione Xamarin.Mac. 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[Utilizzo dei database](~/mac/app-fundamentals/databases.md)

Questo articolo illustra l'uso della codifica chiave-valore e l'osservazione chiave-valore per consentire data binding con accesso diretto ai database SQLite agli elementi dell'interfaccia utente nella Interface Builder di Xcode. Viene inoltre illustrata l'utilizzo di SQLite.NET ORM per fornire l'accesso ai dati SQLite.

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[Utilizzo di copia e incolla](~/mac/app-fundamentals/copy-paste.md)

Questo articolo illustra l'uso del tavolo di montaggio per fornire copia e incolla in un'applicazione Xamarin.Mac. Viene illustrato come usare i tipi di dati standard che possono essere condivisi tra più app e come supportare i dati personalizzati all'interno di un'app give.

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[Sandboxing di un'app Xamarin.Mac](~/mac/app-fundamentals/sandboxing.md)

Questo articolo illustra il sandboxing di un'applicazione Xamarin.Mac per il rilascio nell'App Store. Copre tutti gli elementi che entrano in sandboxing: directory del contenitore, diritti, autorizzazioni definite dall'utente, separazione dei privilegi e imposizione del kernel.

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[Riproduzione di suoni con AVAudioPlayer](~/mac/app-fundamentals/sounds.md)

Questo articolo illustra come usare una classe helper per controllare la riproduzione di suoni usando un AVAudioPlayer.

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[Segnalazione di bug](~/mac/app-fundamentals/troubleshooting.md)

A volte si rimane bloccati quando si lavora a un progetto, in quanto non è possibile ottenere un'API per lavorare nel modo desiderato o nel tentativo di aggirare un bug. L'obiettivo di Novell è quello di poter scrivere le applicazioni per dispositivi mobili e desktop e sono state fornite alcune risorse per aiutare.
