---
title: Nozioni fondamentali sulle applicazioni Xamarin.iOS
description: Questo documento contiene collegamenti a diverse guide che descrivono i concetti fondamentali per lo sviluppo di Xamarin.iOS, ad esempio sicurezza del trasporto delle app, background, eventi e Threading.
ms.prod: xamarin
ms.assetid: 608403AE-B09F-4D9C-8F59-F9DE9F0B1CF1
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 06/21/2017
ms.openlocfilehash: 0ccdde29183645b93831b7261909714f9baf3fa4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73010019"
---
# <a name="xamarinios-application-fundamentals"></a>Nozioni fondamentali sulle applicazioni Xamarin.iOS

Questa sezione fornisce una guida su alcune delle attività più comuni o sui concetti che gli sviluppatori devono tenere presenti durante lo sviluppo di applicazioni Xamarin.iOS (in precedenza MonoTouch).

## <a name="accessibilityiosapp-fundamentalsaccessibilitymd"></a>[Accessibilità](~/ios/app-fundamentals/accessibility.md)

Questo documento descrive le varie API e gli strumenti che è possibile usare per creare applicazioni accessibili al maggior numero possibile di utenti.

## <a name="app-transport-securityiosapp-fundamentalsatsmd"></a>[ATS (App Transport Security)](~/ios/app-fundamentals/ats.md)

In questo articolo vengono introdotte le modifiche di sicurezza applicate dalla sicurezza del trasporto app in un'app iOS 9 e il significato per i progetti Xamarin.iOS, che includerà le opzioni di configurazione di ATS e verrà illustrato come rifiutare esplicitamente ATS, se necessario. Poiché ATS è abilitato per impostazione predefinita, eventuali connessioni Internet non sicure genereranno un'eccezione nelle app iOS 9 (a meno che non sia stata consentita in modo esplicito).

## <a name="backgroundingiosapp-fundamentalsbackgroundingindexmd"></a>[Elaborazione in background](~/ios/app-fundamentals/backgrounding/index.md)

L'elaborazione in background o in background è il processo che consente alle applicazioni di eseguire le attività in background mentre un'altra applicazione viene eseguita in primo piano. Questa guida funge da introduzione all'elaborazione in background in iOS.

## <a name="creating-ios-applications-in-codeiosapp-fundamentalsios-code-onlymd"></a>[Creazione di applicazioni iOS nel codice](~/ios/app-fundamentals/ios-code-only.md)

Questo articolo esamina come creare applicazioni iOS interamente nel codice usando Visual Studio e Visual Studio per Mac. Viene illustrato come iniziare da un modello di progetto vuoto per creare una schermata dell'applicazione in un controller creando una gerarchia di viste da UIKit. Viene quindi illustrato come creare visualizzazioni personalizzate che possono essere caricate in un controller.

## <a name="exception-marshalingiosplatformexception-marshalingmd"></a>[Marshalling delle eccezioni](~/ios/platform/exception-marshaling.md)

Viene descritto come eseguire il marshalling di Objective-C e delle eccezioni gestite tra frame nativi e gestiti.

## <a name="events-protocols-and-delegatesiosapp-fundamentalsdelegates-protocols-and-eventsmd"></a>[Eventi, protocolli e delegati](~/ios/app-fundamentals/delegates-protocols-and-events.md)

Questo articolo presenta le principali tecnologie iOS usate per ricevere callback e per popolare i controlli dell'interfaccia utente con i dati. Queste tecnologie sono eventi, protocolli e delegati. in questo articolo vengono illustrati i singoli elementi e il modo in C#cui vengono utilizzati. Viene illustrato il modo in cui Xamarin.iOS usa i controlli iOS per esporre eventi .NET noti, nonché il modo in cui Xamarin.iOS fornisce supporto per concetti di Objective-C, ad esempio protocolli e delegati ( C# i delegati Objective-c non devono essere confusi con i delegati). Questo articolo fornisce anche esempi che illustrano come usare i protocolli come base per i delegati Objective-C e negli scenari non delegati.

## <a name="working-with-the-file-systemiosapp-fundamentalsfile-systemmd"></a>[Utilizzo del file system](~/ios/app-fundamentals/file-system.md)

Xamarin.iOS può usare le stesse classi System.IO per lavorare con i file e le directory in iOS da usare in qualsiasi applicazione .NET. Tuttavia, nonostante le classi e i metodi noti, iOS implementa alcune restrizioni sui file che è possibile creare o a cui si accede e fornisce anche funzionalità speciali per determinate directory. In questo articolo vengono descritte queste restrizioni e funzionalità e viene illustrato il funzionamento dell'accesso ai file in un'applicazione Xamarin.iOS.

## <a name="working-with-imagesiosapp-fundamentalsimages-iconsindexmd"></a>[Uso delle immagini](~/ios/app-fundamentals/images-icons/index.md)

Questo articolo illustra come usare le immagini in Xamarin.iOS, sia immagini di supporto dell'applicazione (ad esempio icone, caricamento di immagini e così via) che immagini all'interno delle applicazioni, ad esempio le immagini applicate ai controlli. Viene inoltre illustrato come utilizzare Visual Studio per Mac per incorporare immagini e come interagire con le immagini dal codice.

## <a name="localizationiosapp-fundamentalslocalizationindexmd"></a>[Localizzazione](~/ios/app-fundamentals/localization/index.md)

Questa guida illustra l'aggiunta di codifiche a un'applicazione Xamarin.iOS per supportare l'internazionalizzazione.

## <a name="working-with-property-listsiosapp-fundamentalsindexmd"></a>[Utilizzo degli elenchi di proprietà](~/ios/app-fundamentals/index.md)

Questo documento introduce l'editor grafico e avanzato delle proprietà (estensione plist) di Visual Studio per Mac per l'uso di info. plist e dei diritti. plist. Viene illustrata l'impostazione delle icone e delle immagini di avvio per l'applicazione iOS e viene illustrato come specificare le funzionalità delle app (diritti) dall'interno Visual Studio per Mac.

## <a name="working-with-security-and-privacyiosapp-fundamentalssecurity-privacymd"></a>[Uso della sicurezza e della privacy](~/ios/app-fundamentals/security-privacy.md)

Apple ha apportato diversi miglioramenti alla sicurezza e alla privacy in iOS 10 (e versioni successive) che aiuteranno lo sviluppatore a migliorare la sicurezza delle app e garantire la privacy degli utenti finali. In questo articolo viene illustrata l'implementazione di queste funzionalità in un'app Xamarin.iOS.

## <a name="threadingiosapp-fundamentalsthreadingmd"></a>[Threading](~/ios/app-fundamentals/threading.md)

Questo articolo illustra il threading in un'applicazione Xamarin.iOS e comunica un po' sul pool di thread .NET, sulle applicazioni reattive e Garbage Collection.

## <a name="touchiosapp-fundamentalstouchindexmd"></a>[Tocco](~/ios/app-fundamentals/touch/index.md)

Gli schermi touch su molti dei dispositivi attuali consentono agli utenti di interagire in modo rapido ed efficiente con i dispositivi in modo naturale e intuitivo. Questa interazione non è limitata solo al rilevamento semplice del tocco: è possibile utilizzare anche i movimenti. Ad esempio, il gesto di pinch-to-zoom è un esempio molto comune: pizzicando una parte dello schermo con due dita, l'utente può ingrandire o ridurre. Questa guida esamina il tocco e i movimenti in iOS.

## <a name="working-with-user-defaultsiosapp-fundamentalsuser-defaultsmd"></a>[Utilizzo delle impostazioni predefinite dell'utente](~/ios/app-fundamentals/user-defaults.md)

La classe `NSUserDefaults` fornisce un modo per le app e le estensioni iOS per interagire a livello di codice con il sistema predefinito a livello di sistema. Usando il sistema predefinito, l'utente può configurare il comportamento o lo stile di un'app per soddisfare le preferenze (in base alla progettazione dell'app). Ad esempio, per presentare i dati nelle misure metrica rispetto a quelle imperiali oppure selezionare un tema dell'interfaccia utente specifico.
