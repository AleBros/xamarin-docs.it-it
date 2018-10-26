---
title: Modifiche di Framework aggiuntivi watchOS 3
description: Questo documento descrive varie modifiche framework introdotte con watchOS 3 e come utilizzarle in Xamarin. Vengono descritti i dati principali, il movimento di Core, Foundation, HealthKit, HomeKit, PassKit e UIKit.
ms.prod: xamarin
ms.assetid: FE93796E-F699-4B14-B37D-D39F9D48E81E
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 03/17/2017
ms.openlocfilehash: 745c39dab1f73870ce036791434ed9a0b05d681b
ms.sourcegitcommit: e268fd44422d0bbc7c944a678e2cc633a0493122
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2018
ms.locfileid: "50122621"
---
# <a name="additional-watchos-3-frameworks-changes"></a>Modifiche di Framework aggiuntivi watchOS 3

_Questo articolo illustra le modifiche aggiuntive, secondarie o miglioramenti alle infrastrutture esistenti per watchOS 3._

Oltre alle modifiche principali ai dispositivi iOS, Apple ha apportato modifiche e miglioramenti per diversi framework esistente in watchOS 3.


## <a name="core-data"></a>Dati principali

I miglioramenti seguenti sono apportate al framework Core Data per watch OS 3:

- Radice [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) oggetti supporta simultaneo di eventi di errore e il recupero senza serializzazione.
- Il [NSPersistentStoreCoordinator](https://developer.apple.com/reference/coredata/nspersistentstorecoordinator) classe gestisce un pool di archivi dati di SQLite.
- Il [NSManagedObjectContext](https://developer.apple.com/reference/coredata/nsmanagedobjectcontext) oggetti con gli archivi dati di SQLite nel supporto della modalità di registrazione log write-AHEAD di nuova generazione di query in cui i contesti di oggetto gestito (MOC) possono essere aggiunti alle versioni di database specifico per il recupero futuro delle funzionalità e generare un errore per le transazioni.
- Utilizzo di alto livello `NSPersistenceContainer` per fare riferimento al `NSPersistentStoreCoordinator`, [NSManagedObjectModel](https://developer.apple.com/reference/coredata/nsmanagedobjectmodel) e altre risorse di configurazione di Core Data.
- Sono stati aggiunti alcuni nuovi metodi pratici per `NSManagedObject` rendendo più semplice eseguire operazioni di recupero e creare sottoclassi.

Per altre informazioni, vedere di Apple [riferimento a Framework di Core Data](https://developer.apple.com/reference/coredata).


## <a name="core-motion"></a>Movimento di core

I miglioramenti seguenti sono apportate al framework del movimento di Core per watch OS 3:

- Il nuovo evento del movimento del dispositivo usa l'accelerometro e il Giroscopio per fornire aggiornamenti lo spostamento e l'orientamento. l'app è possibile registrarsi per questo aggiornamento (in base alle tariffe di fino a 100Hz).
- Il nuovo evento Pedometer consente veloci e notifiche in tempo reale quando l'utente mette in pausa e viene ripresa l'esecuzione. Usare la [CMPedometer](https://developer.apple.com/reference/coremotion/cmpedometer) per registrare gli eventi pedometer foreground o background.


## <a name="foundation"></a>Foundation

I miglioramenti seguenti sono apportate al framework Foundation per watch OS 3:

- Usare le nuove [NSDateInterval](https://developer.apple.com/reference/foundation/nsdateinterval) classe per rendere i calcoli degli intervalli di data e ora, ad esempio le durate, per il confronto di intervalli e i test per le intersezioni di intervallo.
- Diverse nuove proprietà sono stati aggiunti per il [NSLocal](https://developer.apple.com/reference/foundation/nslocale) classe di acquisire informazioni locali e i formati di visualizzazione disponibile.
- Usare le nuove [NSMeasuerment](https://developer.apple.com/reference/foundation/nsmeasurement) classe convertire tra diverse unità di misura (UDM) o eseguire calcoli sui valori di unità diversa.
- Usare le nuove [NSMeasurementFormatter](https://developer.apple.com/reference/foundation/nsmeasurementformatter) classe per formattare le misurazioni localizzate per la visualizzazione all'utente finale.
- Usare le nuove [NSUnit](https://developer.apple.com/reference/foundation/nsunit) e [NSDimension](https://developer.apple.com/reference/foundation/nsdimension) classi per la rappresentazione di unità specifica.


## <a name="healthkit"></a>HealthKit

I miglioramenti seguenti sono apportate al framework HealthKit per watch OS 3:

- Usare le nuove [HKWorkoutConfiguration](https://developer.apple.com/reference/healthkit/hkworkoutconfiguration) classe per specificare le `ActivityType` e `LocationType` di accinge.
- Il nuovo [HKWheelchairUseObject](https://developer.apple.com/reference/healthkit/hkwheelchairuseobject) e il `WheelchairUse` metodo per il [HKHealthStore](https://developer.apple.com/reference/healthkit/hkhealthstore) classe sono state aggiunte per l'utilizzo con sedia correlati i dati di integrità.
- Sono state aggiunte nuove chiavi di metadati per i tipi di meteo (come `HKWeatherConditionClear` e `HKWeatherConditionCloudy`) e i tipi per gli allenamenti (ad esempio `HKWorkoutActivityTypeFlexibility` e `HKWorkoutActivityTypeWheelchairRunPace`) sono state aggiunte.


## <a name="homekit"></a>HomeKit

I miglioramenti seguenti sono apportate al framework HomeKit per watch OS 3:

- Aggiunta la possibilità di visualizzare e interagire con HomeKit connesso IP fotocamere.
- Aggiunti alcuni nuovi servizi e le caratteristiche.
- Aggiungere più contesto e la configurazione degli accessori dei servizi primari e servizi di collegamento.


## <a name="passkit"></a>PassKit

I miglioramenti seguenti sono apportate al framework di PassKit per espressioni di controllo 3 del sistema operativo:

- Si espande il framework per supportare i pagamenti in-app sicuri sul Apple Watch di beni e servizi.
- Le classi seguenti sono ora disponibili: [PKPayment](https://developer.apple.com/reference/passkit/pkpayment), [PKPaymentMethod](https://developer.apple.com/reference/passkit/pkpaymentmethod), [PKPaymentRequest](https://developer.apple.com/reference/passkit/pkpaymentrequest) e [PKPaymentToken](https://developer.apple.com/reference/passkit/pkpaymenttoken)


## <a name="uikit"></a>UIKit

I miglioramenti seguenti sono apportate al framework UIKit per watch OS 3:

- Per supportare tipi dinamici in etichette, usano i nuovi campi di testo e caselle di testo `PreferredFontForTextStyle` metodo di `UIFont` classe.
- Il `ColorWithDisplayP3` metodo è stato aggiunto per supportare l'ampia gamma di colori.


## <a name="related-links"></a>Collegamenti correlati

- [Guida introduttiva (esempio)](https://developer.xamarin.com/samples/monotouch/WatchKit/)
- [Novità in watchOS 3](https://developer.apple.com/library/prerelease/content/releasenotes/General/WhatsNewInwatchOS/Articles/watchOS3.html#//apple_ref/doc/uid/TP40017085-SW1)
