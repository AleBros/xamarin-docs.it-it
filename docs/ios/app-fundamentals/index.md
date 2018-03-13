---
title: Nozioni fondamentali sulle applicazioni
description: Concetti di base dell'applicazione
ms.topic: article
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/21/2017
ms.openlocfilehash: f6c0bb75327f0c14d314a0e7ad9d114fd17c2a57
ms.sourcegitcommit: 0fdb243b46cf21be47584900805cadcd077121bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="application-fundamentals"></a>Nozioni fondamentali sulle applicazioni

In questa sezione viene fornita una Guida su alcune delle più comuni attività di cose o concetti che gli sviluppatori devono tenere conto durante lo sviluppo di applicazioni di xamarin (in precedenza MonoTouch).

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[ATS (App Transport Security)](~/ios/app-fundamentals/ats.md)

In questo articolo presenterà le modifiche di sicurezza che applica la sicurezza del trasporto App in un'app per iOS 9 e questo significa che per i progetti di xamarin. IOS, illustra le opzioni di configurazione AT e che verrà applicata come rifiutare esplicitamente AT, se necessario. Poiché AT è abilitato per impostazione predefinita, tutte le connessioni internet non sicure genererà un'eccezione nelle app di iOS 9 (a meno che non è stato esplicitamente autorizzato).


## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[Elaborazione in background](~/ios/app-fundamentals/backgrounding/index.md)

L'elaborazione o backgrounding di sfondo è il processo di consentendo alle applicazioni di eseguire le attività in background mentre un'altra applicazione è in esecuzione in primo piano. Questa guida viene utilizzato come un'introduzione all'elaborazione in iOS in background.


## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[Gli eventi, protocolli e delegati](~/ios/app-fundamentals/delegates-protocols-and-events.md)

In questo articolo presenta le tecnologie di iOS chiave utilizzate per ricevere i callback e compilare i controlli dell'interfaccia utente con i dati. Queste tecnologie sono gli eventi, protocolli e delegati. In questo articolo viene descritto ognuno di questi è e ogni utilizzo da c#. Viene illustrato come xamarin utilizza i controlli di iOS per esporre familiarità eventi di .NET, nonché come xamarin fornisce il supporto per i concetti di Objective-C, quali protocolli e delegati (delegati Objective-C non devono essere confusi con i delegati in c#). Inoltre, in questo articolo fornisce esempi che illustrano come protocolli vengono utilizzati sia come base per i delegati Objective-C e in scenari non delegato.

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Threading](~/ios/app-fundamentals/threading.md)

In questo articolo illustra threading in un'applicazione di xamarin. IOS e si indica un po' il pool di thread .NET, applicazioni reattive e operazione di garbage collection.&nbsp;

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[Uso delle immagini](~/ios/app-fundamentals/images-icons/index.md)

In questo articolo viene illustrato come utilizzare le immagini in xamarin. IOS, le immagini di supporto dell'applicazione (ad esempio icone, il caricamento di immagini e così via) e immagini all'interno di applicazioni (ad esempio immagini applicate ai controlli). Descrive inoltre come utilizzare Visual Studio per Mac per incorporare immagini, nonché come interagire con le immagini dal codice.

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[Utilizzo degli elenchi di proprietà](~/ios/app-fundamentals/index.md)

Questo documento introduce Visual Studio per l'editor dell'elenco (con estensione plist) delle proprietà avanzate e con interfaccia grafica del Mac per l'utilizzo di Info. plist ed Entitlements.plist. Viene illustrata l'impostazione di icone e le immagini per l'applicazione iOS e viene illustrato come specificare le funzionalità delle app (diritti) da Visual Studio per Mac.

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[Uso del file system](~/ios/app-fundamentals/file-system.md)

Xamarin è possibile utilizzare le stesse classi System.IO per lavorare con file e directory in iOS che si utilizzerebbe in qualsiasi applicazione .NET. Tuttavia, nonostante le familiari classi e i metodi, iOS implementa alcune restrizioni sui file che è possono creare o accedere e fornisce anche funzionalità speciali per determinate directory. In questo articolo descrive queste restrizioni e funzionalità e viene illustrato il funzionamento di accesso ai file in un'applicazione di xamarin. IOS.

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[Creazione di applicazioni iOS nel codice](~/ios/app-fundamentals/ios-code-only.md)

In questo articolo viene illustrato come creare applicazioni iOS interamente in codice con Visual Studio e Visual Studio per Mac. Viene illustrato come avviare da un modello di progetto vuoto per creare una schermata dell'applicazione in un controller tramite la creazione di una gerarchia di viste da UIKit. Quindi, illustra come creare visualizzazioni personalizzate che possono essere caricate in un controller.

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[Utilizzo di impostazioni predefinite dell'utente](~/ios/app-fundamentals/user-defaults.md)

La `NSUserDefaults` classe fornisce un modo per iOS App e le estensioni per interagire a livello di codice con il sistema predefinito a livello di sistema. Tramite il sistema per impostazione predefinita, l'utente può configurare il comportamento di un'app o gli stili per soddisfare le proprie preferenze (in base alla struttura dell'app). Ad esempio, per presentare i dati in Visual Studio metrica misurazioni imperiale o selezionare un tema dell'interfaccia utente specificato.

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[Tocco](~/ios/app-fundamentals/touch/index.md)

Touch screen su molti dei dispositivi moderni consente agli utenti di interagire in modo rapido ed efficiente con i dispositivi in modo naturale e intuitivo. Questa interazione non è limitata solo per il rilevamento di tocco semplice: è possibile utilizzare anche i movimenti. Ad esempio, il movimento zoom con avvicinamento delle dita è un esempio molto comune: da una parte della schermata con due dita che l'utente è possibile ingrandire o ridurre di compressione. Questa guida viene esaminato tocco e i movimenti in iOS.

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[Utilizzo di sicurezza e Privacy](~/ios/app-fundamentals/security-privacy.md)

Apple ha migliorato notevolmente sia sicurezza e privacy in iOS 10 (e versioni successive) che lo sviluppatore consente di migliorare la sicurezza delle proprie App e garantire la privacy dell'utente finale. Questo articolo vengono descritte l'implementazione di queste funzionalità in un'app xamarin. IOS.

##  <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[Localizzazione](~/ios/app-fundamentals/localization/index.md)

Questa guida vengono illustrate l'aggiunta di codifiche a un'applicazione di xamarin. IOS per il supporto di internazionalizzazione.