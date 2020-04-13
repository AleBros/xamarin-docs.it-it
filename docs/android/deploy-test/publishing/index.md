---
title: Pubblicazione di un'applicazione
ms.prod: xamarin
ms.assetid: 51E19000-040A-2B74-C462-EC57C617085C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: 3062afda82be2b45146687b9d77210a42f8f55fa
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487958"
---
# <a name="publishing-an-application"></a>Pubblicazione di un'applicazione

Quando viene creata un'ottima applicazione, tutti vogliono usarla. Questa sezione illustra i passaggi necessari per la distribuzione pubblica di un'applicazione creata con Xamarin.Android tramite canali quali la posta elettronica, un server Web privato, Google Play o Amazon Appstore per Android.

## <a name="overview"></a>Panoramica

Il passaggio finale dello sviluppo di un'applicazione Xamarin.Android è la pubblicazione dell'applicazione stessa. La pubblicazione è il processo con cui un'applicazione Xamarin.Android viene compilata in modo che gli utenti possano installarla nei propri dispositivi. Questo processo comporta due attività fondamentali:

- **Preparazione per** &ndash; la pubblicazione Viene creata una versione finale dell'applicazione che può essere distribuita ai dispositivi Android (vedere Preparazione di [un'applicazione per](~/android/deploy-test/release-prep/index.md) la versione per ulteriori informazioni sulla preparazione del rilascio).

- **** Distribuzione&ndash;: la versione di rilascio di un'applicazione viene resa disponibile tramite uno o più dei diversi canali di distribuzione.

Il diagramma seguente illustra i passaggi relativi alla pubblicazione di un'applicazione Xamarin.Android:

[![Creare e distribuire diagrammi di flusso](images/build-and-deploy-steps.png)](images/build-and-deploy-steps.png#lightbox)

Come si può notare dal diagramma precedente, la preparazione è la stessa indipendentemente dal metodo di distribuzione usato. Per rilasciare un'applicazione Android agli utenti sono disponibili diversi metodi:

- **** Tramite un sito Web&ndash;: un'applicazione Xamarin.Android può essere resa disponibile per il download in un sito Web, al cui interno gli utenti possono fare clic su un collegamento per installare l'applicazione.
- **** Tramite posta elettronica&ndash;: gli utenti possono installare un'applicazione Xamarin.Android dalla posta elettronica personale. L'applicazione viene installata quando l'allegato viene aperto con un dispositivo Android.
- **Attraverso un mercato** &ndash; Ci sono diversi mercati delle applicazioni che esistono per la distribuzione, come [Google Play](https://play.google.com/) o Amazon App Store [per Android](https://www.amazon.com/mobile-apps/b?ie=UTF8&node=2350149011) .

L'uso di marketplace affermati è il modo più comune di pubblicare un'applicazione, dato che questi sono in grado di offrire la copertura di mercato più ampia e il controllo più solido sulla distribuzione. La pubblicazione di un'applicazione tramite un marketplace, tuttavia, richiede un impegno maggiore.

Un'applicazione Xamarin.Android può essere distribuita contemporaneamente da più canali. Un'applicazione può essere pubblicata, ad esempio, in Google Play e in Amazon Appstore per Android, e può anche essere scaricata da un server Web.

Gli altri due metodi di distribuzione (download e posta elettronica) sono molto utili per un subset controllato di utenti, ad esempio in un ambiente aziendale o per un'applicazione destinata solo a un gruppo ridotto o ben definito di utenti.
La distribuzione tramite server e quella tramite posta elettronica sono anche modelli di pubblicazione più semplici e richiedono una preparazione meno complessa per la pubblicazione.

Il programma Amazon per la distribuzione di app per dispositivi mobili consente agli sviluppatori di distribuire e vendere le proprie applicazioni in Amazon. Per individuare e acquistare app da dispositivi Android personali, gli utenti possono usare l'applicazione Amazon Appstore. Di seguito è riportato uno screenshot di Amazon Appstore in esecuzione in un dispositivo Android:

Google Play è probabilmente il marketplace più completo e famoso per le applicazioni Android. Google Play consente agli utenti di individuare, scaricare, valutare e acquistare le applicazioni con un clic su una sola icona, dal dispositivo o dal computer. Google Play mette anche a disposizione alcuni strumenti che semplificano l'analisi delle vendite e le tendenze di mercato e che consentono di determinare i dispositivi e gli utenti autorizzati a scaricare l'applicazione. Di seguito è riportato uno screenshot di Google Play in esecuzione in un dispositivo Android:

[![Schermata di Google Play](images/google-play-app.png)](images/google-play-app.png#lightbox)

Questa sezione illustra come caricare l'applicazione in uno store, ad esempio in Google Play, insieme al materiale promozionale appropriato. La sezione descrive anche i file di espansione APK, con una panoramica concettuale di che cosa sono e di come funzionano, e i servizi di gestione delle licenze Google. Vengono infine presentati metodi di distribuzione alternativi, ad esempio l'uso di un server Web HTTP, della semplice distribuzione tramite posta elettronica e di Amazon Appstore per Android.

## <a name="related-links"></a>Collegamenti correlati

- [HelloWorldPublishing (sample)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/helloworldpublishing) (HelloWorldPublishing - Esempio)
- [Processo di compilazione](~/android/deploy-test/building-apps/build-process.md)
- [Collegamento](~/android/deploy-test/linker.md)
- [Ottenere una chiave API di Google Maps](~/android/platform/maps-and-location/maps/obtaining-a-google-maps-api-key.md)
- [Distribuire tramite Visual Studio App CenterDeploy via Visual Studio App Center](https://docs.microsoft.com/appcenter/distribution/stores/googleplay)
- [Application Signing](https://source.android.com/security/apksigning/) (Firma dell'applicazione)
- [Pubblicazione in Google Play](https://developer.android.com/distribute/googleplay/publish/index.html)
- [Licenze di applicazioni Google](https://developer.android.com/guide/google/play/licensing/index.html)
- [Android.Play.ExpansionLibrary](https://github.com/mattleibow/Android.Play.ExpansionLibrary)
- [Portale di distribuzione di app per dispositivi mobili](https://developer.amazon.com/welcome.html)
- [Domande frequenti sulla distribuzione di app per dispositivi mobili Amazon](https://developer.amazon.com/help/faq.html)
