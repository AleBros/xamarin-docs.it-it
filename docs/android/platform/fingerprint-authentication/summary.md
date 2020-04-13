---
title: Indicazioni per l'autenticazione delle impronte digitali
ms.prod: xamarin
ms.assetid: B40332CC-8123-4150-B47E-996214388842
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/16/2018
ms.openlocfilehash: e955d4f96724bd5682e7d0e6db2c36fa1b7810f4
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027423"
---
# <a name="fingerprint-authentication-guidance"></a>Indicazioni per l'autenticazione delle impronte digitali

## <a name="fingerprint-authentication-guidance"></a>Indicazioni per l'autenticazione delle impronte digitali

Ora che abbiamo visto i concetti e le API che circondano l'autenticazione delle impronte digitali di Android 6.0, diamo un'esitazione di alcuni consigli generali per l'uso delle API delle impronte digitali.

1. Usare le API di **compatibilità v4** &ndash; della libreria di supporto Android Semplifica il codice dell'applicazione rimuovendo il controllo API dal codice e consentirà a un'applicazione di essere destinata alla maggior parte dei dispositivi possibili.
2. **Fornire alternative** &ndash; all'autenticazione delle impronte digitali delle impronte digitali è un ottimo modo rapido per un'applicazione di autenticare un utente, tuttavia, non si può presumere che funzionerà sempre o sarà disponibile. È possibile che lo scanner di impronte digitali non riesca, l'obiettivo sia sporco, che l'utente non abbia configurato il dispositivo per l'utilizzo dell'autenticazione delle impronte digitali o che le impronte digitali siano scomparse. È anche possibile che l'utente non desideri utilizzare l'autenticazione delle impronte digitali con l'applicazione. Per questi motivi, un'applicazione Android deve fornire un processo di autenticazione alternativo, ad esempio nome utente e password.
3. **Utilizzare l'icona** &ndash; delle impronte digitali di Google Tutte le applicazioni devono utilizzare la stessa icona di impronte digitali fornita da Google. L'uso di un'icona standard rende facile per gli utenti Android riconoscere dove in app viene utilizzata l'autenticazione delle impronte digitali: 
    
    ![Icona dell'impronta digitale Android](summary-images/ic-fp-40px.png)
    
4. **Notifica all'utente** &ndash; Un'applicazione deve visualizzare un tipo di notifica all'utente che lo scanner di impronte digitali è attivo e in attesa di un tocco o scorrimento rapido. 

## <a name="summary"></a>Riepilogo

L'autenticazione delle impronte digitali è un ottimo modo per consentire a un'applicazione Xamarin.Android di verificare rapidamente gli utenti, semplificando l'interazione degli utenti con funzionalità sensibili come gli acquisti in-app. Questa guida ha discusso i concetti e il codice necessari per incorporare le API di impronta digitale Android 6.0 nell'applicazione Xamarin.Android.This guide discussed the concepts and code that is required to incorporate the Android 6.0 fingerprint API's in your Xamarin.Android application.

Per prima cosa abbiamo discusso `FingerprintManager` l'API delle impronte digitali, (e `FingerprintManagerCompat`). È stato esaminato `FingerprintManager.AuthenticationCallbacks` il modo in cui la classe astratta deve essere estesa da un'applicazione e utilizzata come intermediario tra l'hardware delle impronte digitali e l'applicazione stessa. Poi abbiamo esaminato come verificare l'integrità dei `Cipher` risultati dello scanner di impronte digitali utilizzando un oggetto Java. Infine, abbiamo toccato un po' di test descrivendo come registrare un'impronta digitale su un dispositivo e usando **adb** per simulare uno scorrimento rapido di un'impronta digitale su un emulatore. 

Se non è già stato fatto, si dovrebbe guardare [l'applicazione di esempio](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide) che accompagna questa guida. Il fingerprint Dialog Sample è stato portato da Java a Xamarin.Android e fornisce un altro esempio su come aggiungere l'autenticazione delle impronte digitali a un'applicazione Android.The [Fingerprint Dialog Sample](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog) has been ported from Java to Xamarin.Android and provides another example on how to add fingerprint authentication to an Android application.

## <a name="related-links"></a>Collegamenti correlati

- [App di esempio della guida alle impronte digitali](https://github.com/xamarin/monodroid-samples/tree/master/FingerprintGuide)
- [Esempio di finestra di dialogo impronta digitale](https://docs.microsoft.com/samples/xamarin/monodroid-samples/android-m-fingerprintdialog)
- [Icone Impronta Digitale](https://raw.githubusercontent.com/xamarin/monodroid-samples/master/FingerprintGuide/FingerprintSampleApp/Resources/drawable-hdpi/ic_fp_40px.png)
