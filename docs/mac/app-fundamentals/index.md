---
title: Nozioni fondamentali dell'applicazione xamarin. Mac
description: Questo documento include collegamenti alle guide che descrivono vari concetti necessari per comprendere quando si sviluppano applicazioni xamarin. Mac.
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: lobrien
ms.author: laobri
ms.date: 12/17/2015
ms.openlocfilehash: 376286b73c92cba40de183043b86cb4ffb5e699d
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61085305"
---
# <a name="xamarinmac-application-fundamentals"></a>Nozioni fondamentali dell'applicazione xamarin. Mac

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[Modelli e idiomi comuni](~/mac/app-fundamentals/patterns.md)

In tutta l'API Apple esposte tramite c#, alcuni modelli e idiomi ad avviarsi in modo continuativo. Se si ha esperienza con la programmazione con xamarin. IOS, questi potrebbero apparire familiari. Documentazione spesso farà riferimento a questi modelli e idiomi più volte, in modo da avere una conoscenza approfondita di essi consentono di avere senso della documentazione che trova.

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[Informazioni sulle API Mac](~/mac/app-fundamentals/mac-apis.md)

Per la maggior parte del tempo allo sviluppo con xamarin. Mac, è possibile pensare, leggere e scrivere in c# senza preoccuparsi con le API Objective-C sottostante. Tuttavia, in alcuni casi sarà necessario leggere la documentazione dell'API di Apple, tradurre una risposta di Stack Overflow per una soluzione al problema o confrontare con un esempio esistente.

## <a name="console-appsmacapp-fundamentalsconsolemd"></a>[App console](~/mac/app-fundamentals/console.md)

È anche possibile compilare App console "headless" accedere alle API di macOS native usando xamarin. Mac.

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[Utilizzo di file con estensione xib](~/mac/app-fundamentals/xib.md)

Questo articolo descrive l'uso con i file con estensione xib creati in Interface Builder di Xcode per creare e gestire le interfacce utente per un'applicazione xamarin. Mac.

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[.Storyboard/.xib meno progettazione dell'interfaccia utente](~/mac/app-fundamentals/xibless-ui.md)

Questo articolo illustra la creazione dell'interfaccia utente di un'applicazione xamarin. Mac direttamente dal codice c# senza dover usare Interface Builder di Xcode con i file con estensione xib o storyboard.

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[Utilizzo di immagini](~/mac/app-fundamentals/image.md)

Questo articolo descrive l'uso delle immagini e icone in un'applicazione xamarin. Mac. Viene descritto come creare e gestire le immagini necessarie per creare l'icona dell'applicazione e l'utilizzo di immagini nel codice c# e Interface Builder di Xcode.

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[Data binding e codifica di chiave-valore](~/mac/app-fundamentals/databinding.md)

Questo articolo viene illustrato l'utilizzo chiave-valore di codifica e chiave-valore osservando la possibilità di associazione dati a elementi dell'interfaccia utente in Interface Builder di Xcode. Utilizzando questa tecnica, è notevolmente ridurre la quantità di codice c# che deve essere scritto per l'applicazione xamarin. Mac. 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[Utilizzo dei database](~/mac/app-fundamentals/databases.md)

Questo articolo viene illustrato l'utilizzo chiave-valore di codifica e chiave-valore osservando la possibilità di data binding con accesso diretto ai database SQLite agli elementi dell'interfaccia utente di Interface Builder di Xcode. Viene anche illustrato l'uso di SQLite.NET ORM per fornire accesso ai dati di SQLite.

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[Utilizzo di copia e Incolla](~/mac/app-fundamentals/copy-paste.md)

Questo articolo descrive come usare il tavolo alla invierà copia e Incolla in un'applicazione xamarin. Mac. Viene illustrato come lavorare con i tipi di dati standard che possono essere condivisi tra più App e come supportare dati personalizzati all'interno di un'app offrono.

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[Sandboxing un'app xamarin. Mac](~/mac/app-fundamentals/sandboxing.md)

Questo articolo illustra il sandboxing un'applicazione xamarin. Mac di rilascio di App Store. Viene descritto come tutti gli elementi che costituiscono il sandboxing: directory contenitore, i diritti, determinato dall'utente, la separazione dei privilegi e autorizzazioni l'imposizione del kernel.

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[Riproduzione di suoni con AVAudioPlayer](~/mac/app-fundamentals/sounds.md)

Questo articolo illustra come usare una classe helper per controllare la riproduzione di suoni con AVAudioPlayer un.

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[Segnalazione dei bug](~/mac/app-fundamentals/troubleshooting.md)

In alcuni casi è creato un blocco durante l'uso di un progetto, l'impossibilità di ottenere un'API da usare nel modo desiderato o in sta tentando di risolvere un bug. Sono disponibili alcune risorse utili per il nostro obiettivo in Xamarin è appositamente per la scrittura di applicazioni per dispositivi mobili e desktop in modo corretto.
