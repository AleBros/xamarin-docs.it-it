---
title: Indicazioni sull'autenticazione con impronta digitale
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e955d4f96724bd5682e7d0e6db2c36fa1b7810f4
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027423"
---
# <a name="fingerprint-authentication-guidance"></a>Indicazioni sull'autenticazione con impronta digitale

## <a name="fingerprint-authentication-guidance"></a>Indicazioni sull'autenticazione con impronta digitale

Ora che sono stati illustrati i concetti e le API che racchiudono l'autenticazione con impronta digitale Android 6,0, verranno illustrati alcuni consigli generali per l'uso delle API per le impronte digitali.

1. **Usare le API di compatibilità V4 per la libreria di supporto Android** &ndash; questa operazione semplificherà il codice dell'applicazione rimuovendo il controllo API dal codice e consentendo a un'applicazione di fare riferimento alla maggior parte dei dispositivi possibili.
2. **Fornire alternative all'autenticazione con impronta digitale** &ndash; l'autenticazione con impronta digitale è un modo rapido e rapido per un'applicazione per autenticare un utente. Tuttavia, non è possibile presupporre che funzioni sempre o sia disponibile. È possibile che lo scanner di impronta digitale possa avere esito negativo, che l'obiettivo sia Dirty, che l'utente non abbia configurato il dispositivo per l'uso dell'autenticazione tramite impronta digitale o che le impronte digitali siano mancanti. È anche possibile che l'utente non voglia usare l'autenticazione con impronta digitale con l'applicazione. Per questi motivi, un'applicazione Android deve fornire un processo di autenticazione alternativo, ad esempio nome utente e password.
3. **Usare l'icona dell'impronta digitale di google** &ndash; tutte le applicazioni devono usare la stessa icona di impronta digitale fornita da Google. L'uso di un'icona standard consente agli utenti di Android di riconoscere la posizione in cui viene usata l'autenticazione con impronta digitale delle app: 
    
    ![Icona di impronta digitale Android](summary-images/ic-fp-40px.png)
    
4. **Inviare** una notifica all'utente &ndash; un'applicazione deve visualizzare un tipo di notifica all'utente che lo scanner di impronta digitale è attivo e in attesa di un tocco o di una striscia. 

## <a name="summary"></a>Riepilogo

L'autenticazione con impronta digitale è un ottimo modo per consentire a un'applicazione Xamarin.Android di verificare rapidamente gli utenti, semplificando l'interazione degli utenti con funzionalità sensibili come gli acquisti in-app. Questa guida ha illustrato i concetti e il codice necessari per incorporare l'API per le impronte digitali Android 6,0 nell'applicazione Xamarin.Android.

Prima di tutto abbiamo discusso le API dell'impronta digitale, `FingerprintManager` (e `FingerprintManagerCompat`). È stato esaminato il modo in cui la classe astratta `FingerprintManager.AuthenticationCallbacks` deve essere estesa da un'applicazione e usata come intermediario tra l'hardware dell'impronta digitale e l'applicazione stessa. Si è quindi esaminato come verificare l'integrità dei risultati dello scanner di impronta digitale usando un oggetto Java `Cipher`. Infine, abbiamo toccato un po' di test descrivendo come registrare un'impronta digitale in un dispositivo e usando **ADB** per simulare un swipe impronta digitale su un emulatore. 

Se non è già stato fatto, è necessario esaminare l' [applicazione di esempio](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) che accompagna questa guida. L' [esempio di finestra di dialogo impronta digitale](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog) è stato trasferito da Java a Xamarin.Android e fornisce un altro esempio su come aggiungere l'autenticazione con impronta digitale a un'applicazione Android.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio per la Guida all'impronta digitale](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [Esempio di finestra di dialogo impronta digitale](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Icona impronta digitale](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)
