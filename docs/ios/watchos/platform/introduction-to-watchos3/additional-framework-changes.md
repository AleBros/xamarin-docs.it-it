---
title: Modifiche di Framework aggiuntive watchOS 3
description: Questo documento descrive varie modifiche framework introdotte con watchOS 3 e come utilizzarle in Xamarin. Vengono descritti i dati principali, movimento dei componenti di base, Foundation, HealthKit, HomeKit, PassKit e UIKit.
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: bradumbaugh
ms.author: brumbaug
ms.date: 03/17/2017
ms.openlocfilehash: af44096928c5e543ac99df3faec9f2e9215f666d
ms.sourcegitcommit: ea1dc12a3c2d7322f234997daacbfdb6ad542507
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/05/2018
ms.locfileid: "34791515"
---
# <a name="additional-watchos-3-frameworks-changes"></a>Modifiche di Framework aggiuntive watchOS 3

_Questo articolo vengono illustrate le modifiche aggiuntive, secondarie o miglioramenti alla Framework esistenti per watchOS 3._

Oltre alle modifiche essenziali per iOS, Apple ha apportato modifiche e miglioramenti per diversi Framework esistenti watchOS 3.


## <a name="core-data"></a>Dati di base

I miglioramenti seguenti apportate a framework dati di base per espressioni di controllo del sistema operativo 3:

- Radice [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) oggetti supporta simultaneo di generare un errore e di recupero senza serializzazione.
- Il [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) classe gestisce un pool di archivi dati di SQLite.
- Il [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) oggetti con archivi dati SQLite il supporto di modalità Journal WAL la generazione di query nuove funzionalità in contesti di oggetto gestito (MOC) possono essere aggiunti a versioni specifiche di database per il recupero futuro e generare un errore per le transazioni.
- Utilizzo di alto livello `NSPersistenceContainer` al riferimento di `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e altre risorse di configurazione di dati di base.
- Sono stati aggiunti numerosi nuovi metodi pratici `NSManagedObject` rendendo più semplice eseguire operazioni di recupero e di creare sottoclassi.

Per ulteriori informazioni, vedere Apple [Core dati Framework Reference](https://developer.apple.com/reference/coredata).


## <a name="core-motion"></a>Movimento di core

I miglioramenti seguenti apportate a framework Core movimento per espressioni di controllo del sistema operativo 3:

- Il nuovo evento del dispositivo usa l'accelerometro e Giroscopio per fornire gli aggiornamenti di animazione e l'orientamento. l'app di app può registrare per questo aggiornamento (in base alle tariffe di fino a 100Hz).
- Il nuovo evento Pedometer veloce, consente a notifiche in tempo reale, quando l'utente posiziona e viene ripresa l'esecuzione. Utilizzare il [CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer) per registrare gli eventi pedometer in primo piano o in background.


## <a name="foundation"></a>Foundation

I miglioramenti seguenti apportate a framework Foundation per espressioni di controllo 3 del sistema operativo:

- Usare il nuovo [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe per eseguire calcoli di intervallo di data e ora, ad esempio le durate, per il confronto di intervalli e test per le intersezioni di intervallo.
- Diverse nuove proprietà sono stati aggiunti i [NSLocal](https://developer.apple.com/reference/foundation/nslocale) classe per acquisire informazioni locali e i formati di visualizzazione disponibili.
- Usare il nuovo [NSMeasuerment](https://developer.apple.com/reference/foundation/nsmeasurement) classe per convertire tra diverse unità di misura (UDM) o eseguire calcoli sui valori di unità diversa.
- Usare il nuovo [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) classe per formattare le misurazioni localizzate per la visualizzazione per l'utente finale.
- Usare il nuovo [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) classi per la rappresentazione di unità specifiche.


## <a name="healthkit"></a>HealthKit

I miglioramenti seguenti apportate a framework HealthKit per espressioni di controllo 3 del sistema operativo:

- Usare il nuovo [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) classe per specificare il `ActivityType` e `LocationType` di accinge.
- Il nuovo [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) e `WheelchairUse` metodo il [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) classe sono stati aggiunti per l'utilizzo con sedia integrità dati correlati.
- Nuove chiavi di metadati sono stati aggiunti per i tipi di meteo (ad esempio `HKWeatherConditionClear` e `HKWeatherConditionCloudy`) e tipi allenamenti (ad esempio `HKWorkoutActivityTypeFlexibility` e `HKWorkoutActivityTypeWheelchairRunPace`) sono stati aggiunti.


## <a name="homekit"></a>HomeKit

I miglioramenti seguenti apportate a framework HomeKit per espressioni di controllo 3 del sistema operativo:

- Aggiungere la possibilità di visualizzare e interagire con HomeKit connesso IP fotocamere.
- Aggiunta di numerosi nuovi servizi e le caratteristiche.
- Aggiunta di più contesto e la configurazione degli accessori dei servizi primari e collegare i servizi.


## <a name="passkit"></a>PassKit

I miglioramenti seguenti apportate a framework PassKit per espressioni di controllo 3 del sistema operativo:

- Si espande il framework per supportare i pagamenti protetti nell'applicazione nel Apple Watch di merci fisiche e servizi.
- Le classi seguenti sono ora disponibili: [PKPayment](https://developer.apple.com/reference/passkit/pkpayment), [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod), [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest) e [PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)


## <a name="uikit"></a>UIKit

I miglioramenti seguenti apportate a framework UIKit per espressioni di controllo 3 del sistema operativo:

- Per supportare tipi dinamici nelle etichette, usano i nuovi campi di testo e caselle di testo `PreferredFontForTextStyle` metodo la `UIFont` classe.
- Il `ColorWithDisplayP3` metodo è stato aggiunto per supportare colori ampia.


## <a name="related-links"></a>Collegamenti correlati

- [Guida introduttiva (esempio)](https://developer.xamarin.com/samples/monotouch/WatchKit/)
- [Novità di watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
