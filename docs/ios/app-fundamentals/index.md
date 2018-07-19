---
title: Nozioni fondamentali dell'applicazione xamarin. IOS
description: Questo documento include collegamenti alle varie guide che descrivono i concetti fondamentali per lo sviluppo di xamarin. IOS, ad esempio la sicurezza di trasporto di app, elaborazione in background in, eventi e threading.
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 06/21/2017
ms.openlocfilehash: de337291554e81a2434dcc30c163f4789fc832eb
ms.sourcegitcommit: e98a9ce8b716796f15de7cec8c9465c4b6bb2997
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2018
ms.locfileid: "39111212"
---
# <a name="xamarinios-application-fundamentals"></a>Nozioni fondamentali dell'applicazione xamarin. IOS

In questa sezione fornisce una Guida su alcune delle più comuni attività di cose o concetti che gli sviluppatori devono tenere presenti quando si sviluppano applicazioni xamarin. IOS (in precedenza MonoTouch).

## <a name="accessibilityiosapp-fundamentalsaccessibilitymd"></a>[Accessibilità](~/ios/app-fundamentals/accessibility.md)

Questo documento descrive diverse API e strumenti che possono essere utilizzati per la creazione di applicazioni che sono accessibili a tutti gli utenti possibili.

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[ATS (App Transport Security)](~/ios/app-fundamentals/ats.md)

Questo articolo verrà introdotte le modifiche di sicurezza che applica la sicurezza del trasporto di App in un'app per iOS 9 e cosa significa questo per i progetti xamarin. IOS, verranno illustrati le opzioni di configurazione di ATS e illustrerà come rifiutare esplicitamente di ATS, se necessario. Poiché ATS è abilitato per impostazione predefinita, tutte le connessioni internet non sicuri genererà un'eccezione nelle app di iOS 9 (a meno che non è stata autorizzata in modo esplicito,).

## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[Elaborazione in background](~/ios/app-fundamentals/backgrounding/index.md)

In background di elaborazione o l'elaborazione in background in è il processo di consentire alle applicazioni di eseguire attività in background mentre un'altra applicazione è in esecuzione in primo piano. Questa guida serve come un'introduzione a iOS di elaborazione in background.

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[Creazione di applicazioni iOS nel codice](~/ios/app-fundamentals/ios-code-only.md)

Questo articolo viene illustrato come creare applicazioni iOS interamente in codice con Visual Studio e Visual Studio per Mac. Viene illustrato come avviare da un modello di progetto vuoto per creare una schermata dell'applicazione in un controller creando una gerarchia di visualizzazioni da UIKit. Quindi, viene illustrato come creare viste personalizzate che possono essere caricate in un controller.

## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[Gli eventi, protocolli e delegati](~/ios/app-fundamentals/delegates-protocols-and-events.md)

Questo articolo presenta le tecnologie chiave iOS usate per ricevere i callback e per popolare i controlli dell'interfaccia utente con i dati. Queste tecnologie sono gli eventi, protocolli e delegati. questo articolo illustra gli elementi ciascuno di essi è e come ognuno viene usato da c#. Viene illustrato come xamarin. IOS Usa controlli di iOS per esporre familiare .NET anche gli eventi, come xamarin. IOS fornisce il supporto per i concetti di Objective-C, ad esempio i protocolli e delegati (delegati Objective-C non devono essere confusi con delegati c#). Questo articolo offre anche esempi che illustrano come i protocolli vengono usati sia come base per i delegati di Objective-C e in scenari non delegato.

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[Utilizzo di file system](~/ios/app-fundamentals/file-system.md)

Xamarin. IOS è possibile usare le stesse classi System.IO per lavorare con file e directory in iOS che si utilizzerebbe in qualsiasi applicazione .NET. Tuttavia, nonostante le familiari classi e metodi, iOS implementa alcune restrizioni sui file che possono essere creati o si accede e fornisce anche funzionalità speciali per alcune directory. Questo articolo descrive queste restrizioni e funzionalità e illustra il funzionamento di accesso ai file in un'applicazione xamarin. IOS.

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[Utilizzo di immagini](~/ios/app-fundamentals/images-icons/index.md)

Questo articolo esamina come usare le immagini in xamarin. IOS, le immagini di supporto dell'applicazione (ad esempio icone, il caricamento di immagini e così via) sia immagini all'interno delle applicazioni (ad esempio immagini applicate ai controlli). Viene anche illustrato come usare Visual Studio per Mac per incorporare immagini, nonché come interagire con le immagini dal codice.

## <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[Localizzazione](~/ios/app-fundamentals/localization/index.md)

Questa guida illustra l'aggiunta di codifiche a un'applicazione xamarin. IOS per il supporto di internazionalizzazione.

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[Utilizzo degli elenchi di proprietà](~/ios/app-fundamentals/index.md)

Questo documento presenta Visual Studio per l'editor dell'elenco (con estensione plist) proprietà grafiche e avanzato del Mac per l'utilizzo di Info. plist ed entitlements. plist. Viene illustrata l'impostazione delle icone e immagini per applicazione iOS, esecuzione e viene illustrato come specificare le funzionalità delle app (gli Entitlement) da Visual Studio per Mac.

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[Utilizzo di sicurezza e privacy](~/ios/app-fundamentals/security-privacy.md)

Apple ha introdotto numerosi miglioramenti al sia sicurezza e privacy in iOS 10 (e versioni successive) che consentono allo sviluppatore di migliorare la sicurezza delle proprie App e garantire la privacy dell'utente finale. Questo articolo illustra l'implementazione di queste funzionalità in un'app xamarin. IOS.

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Threading](~/ios/app-fundamentals/threading.md)

Questo articolo illustra il threading in un'applicazione xamarin. IOS e un po' parla il pool di thread .NET, applicazioni reattive e operazioni di garbage collection.

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[Tocco](~/ios/app-fundamentals/touch/index.md)

Touchscreen su molti dei dispositivi moderni consentono agli utenti di interagire in modo rapido ed efficiente con i dispositivi in modo naturale e intuitivo. Questa interazione non è limitata solo al rilevamento di tocco semplice: è possibile usare anche i movimenti. Ad esempio, il movimento di zoom con avvicinamento delle dita è un esempio molto comune, dall'avvicinamento una parte della schermata con due dita, che l'utente può eseguire lo zoom avanti o indietro. Questa guida esamina il tocco e movimenti in iOS.

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[Utilizzo di impostazioni predefinite dell'utente](~/ios/app-fundamentals/user-defaults.md)

Il `NSUserDefaults` classe fornisce un modo per iOS App ed estensioni per interagire a livello di codice con il sistema a livello di sistema predefinito. Tramite il sistema per impostazione predefinita, l'utente può configurare il comportamento di un'app o applicazione di stili per soddisfare le proprie preferenze (in base alla struttura dell'app). Ad esempio, per presentare i dati in Visual Studio metrica misurazioni imperiale o selezionare un tema dell'interfaccia utente specificato.
