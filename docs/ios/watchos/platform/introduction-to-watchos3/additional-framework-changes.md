---
title: Modifiche ai Framework di watchos 3 aggiuntivi
description: Questo documento descrive le varie modifiche del Framework introdotte con watchos 3 e come usarle in Novell. Vengono illustrati i dati di base, Motion core, Foundation, HealthKit, HomeKit, PassKit e UIKit.
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 03/17/2017
ms.openlocfilehash: cd4bc8dbc02a44807ec197d39349971d8f9cd6f9
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2019
ms.locfileid: "70768588"
---
# <a name="additional-watchos-3-frameworks-changes"></a>Modifiche ai Framework di watchos 3 aggiuntivi

_Questo articolo illustra ulteriori modifiche o miglioramenti secondari ai Framework esistenti per watchos 3._

Oltre alle principali modifiche apportate a iOS, Apple ha apportato modifiche e miglioramenti a diversi Framework esistenti in watchos 3.

## <a name="core-data"></a>Dati di base

Sono stati apportati i miglioramenti seguenti al Framework di dati principale per Watch OS 3:

- Gli oggetti [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) radice supportano gli errori e il recupero simultanei senza serializzazione.
- La classe [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) gestisce un pool di archivi dati SQLite.
- Gli oggetti [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) con gli archivi dati SQLite nella modalità Journal Wal supportano la nuova funzionalità di generazione delle query in cui i contesti degli oggetti gestiti (MOC) possono essere aggiunti a versioni specifiche del database per le transazioni di recupero e di errore future.
- Utilizzo di alto livello `NSPersistenceContainer` per `NSPersistentStoreCoordinator`fare riferimento a, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e altre risorse di configurazione dei dati principali.
- Sono stati aggiunti diversi nuovi metodi pratici per `NSManagedObject` semplificare l'esecuzione di operazioni di recupero e creazione di sottoclassi.

Per altre informazioni, vedere la Guida di [riferimento a Core Data Framework](https://developer.apple.com/reference/coredata)di Apple.

## <a name="core-motion"></a>Movimento di base

I miglioramenti seguenti sono stati apportati al Framework di movimento principale per Watch OS 3:

- Il nuovo evento di movimento del dispositivo usa l'accelerometro e il giroscopio per fornire aggiornamenti di movimento e orientamento. L'app può registrarsi per questo aggiornamento (alle tariffe fino a 100Hz).
- Il nuovo evento pedometro consente le notifiche rapide e in tempo reale quando l'utente sospende e riprende l'esecuzione. Usare [CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer) per eseguire la registrazione per gli eventi del pedometro in primo piano o in background.

## <a name="foundation"></a>Foundation

Di seguito sono riportati i miglioramenti apportati al Framework di base per Watch OS 3:

- Usare la nuova classe [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) per creare calcoli di intervallo di data e ora, ad esempio le durate, per confrontare gli intervalli e i test per le intersezioni degli intervalli.
- Sono state aggiunte diverse nuove proprietà alla classe [NSLocal](https://developer.apple.com/reference/foundation/nslocale) per acquisire informazioni locali e i formati di visualizzazione disponibili.
- Usare la nuova classe [NSMeasurement](https://developer.apple.com/reference/foundation/nsmeasurement) per eseguire la conversione tra unità di misura diverse (uom) o eseguire calcoli su valori in UOMs diversi.
- Utilizzare la nuova classe [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) per formattare le misurazioni localizzate per la visualizzazione all'utente finale.
- Utilizzare le nuove classi [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) per la rappresentazione di UOMs specifici.

## <a name="healthkit"></a>HealthKit

Sono stati apportati i miglioramenti seguenti a HealthKit Framework per Watch OS 3:

- Usare la nuova classe [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) per specificare `ActivityType` e `LocationType` di un allenamento.
- Sono stati aggiunti i nuovi [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) e il metodo `WheelchairUse` della classe [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) per lavorare con i dati di integrità correlati a carrozzine.
- Sono state aggiunte nuove chiavi di metadati per i tipi meteorologici `HKWeatherConditionClear` ( `HKWeatherConditionCloudy`ad esempio e) e sono stati `HKWorkoutActivityTypeFlexibility` aggiunti `HKWorkoutActivityTypeWheelchairRunPace`tipi di allenamento (ad esempio e).

## <a name="homekit"></a>HomeKit

Sono stati apportati i miglioramenti seguenti a HomeKit Framework per Watch OS 3:

- È stata aggiunta la possibilità di visualizzare e interagire con le fotocamere IP connesse a HomeKit.
- Sono stati aggiunti diversi nuovi servizi e caratteristiche.
- Aggiunto il contesto e la configurazione degli accessori dei servizi primari e dei servizi di collegamento.

## <a name="passkit"></a>PassKit

Sono stati apportati i miglioramenti seguenti a PassKit Framework per Watch OS 3:

- Espande il Framework per supportare i pagamenti sicuri in-app sul Apple Watch di beni e servizi fisici.
- Sono ora disponibili le seguenti classi: [PKPayment](https://developer.apple.com/reference/passkit/pkpayment), [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod), [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest) e [PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)

## <a name="uikit"></a>UIKit

Sono stati apportati i miglioramenti seguenti a UIKit Framework per Watch OS 3:

- Per supportare il tipo dinamico nelle etichette, i campi `PreferredFontForTextStyle` `UIFont` di testo e le caselle di testo utilizzano il nuovo metodo della classe.
- Il `ColorWithDisplayP3` metodo è stato aggiunto per supportare il colore Wide.

## <a name="related-links"></a>Collegamenti correlati

- [esempi watchos](https://docs.microsoft.com/samples/browse/?products=xamarin&term=Xamarin.iOS%20watchos)
- [Novità di watchos 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
