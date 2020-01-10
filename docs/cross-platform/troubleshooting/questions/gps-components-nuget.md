---
title: Unificazione dei componenti di Google Play Services e NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
author: davidortinau
ms.author: daortin
ms.date: 05/08/2018
ms.openlocfilehash: 0b2fd92eb9157b561708eafe10d341381fa21543
ms.sourcegitcommit: 4691b48f14b166afcec69d1350b769ff5bf8c9f6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2020
ms.locfileid: "75728200"
---
# <a name="unifying-google-play-services-components-and-nuget"></a>Unificazione dei componenti di Google Play Services e NuGet

## <a name="history"></a>Cronologia

Sono stati usati diversi componenti Google Play Services e pacchetti NuGet:

- Google Play Services (Froyo)
- Google Play Services (Gingerbread)
- Google Play Services (ICS)
- Google Play Services (JellyBean)
- Google Play Services (KitKat)

Google effettivamente spedisce solo due file con estensione jar per Google Play Services:

- `google-play-services-froyo.jar`
- `google-play-services.jar`

La discrepanza era dovuta al fatto che gli strumenti non indicavano in modo corretto `aapt.exe` quale sia il livello API di risorsa massimo da usare per una determinata app. Ciò significa che sono stati ricevuti errori di compilazione se si è provato a usare l'associazione Google Play Services (KitKat) a un livello API inferiore, ad esempio Gingerbread.

## <a name="unifying-google-play-services"></a>Unificazione di Google Play Services

Nelle versioni più recenti di Novell. Android, ora si indica `aapt.exe` quale versione massima delle risorse usare, quindi questo problema si interrompe.

Ciò significa che non esiste un motivo reale per avere pacchetti separati per Gingerbread/ICS/JellyBean/KitKat (Tuttavia, è ancora necessario un binding separato per Froyo poiché si tratta di un file con estensione jar diverso).

Per semplificare le cose per gli sviluppatori, abbiamo ora unificato i nostri componenti e i pacchetti NuGet in due:

- Google Play Services (Froyo) (binds `google-play-services-froyo.jar`)
- Google Play Services (associazioni `google-play-services.jar`)

### <a name="which-one-should-be-used"></a>Quale si deve usare?

In quasi tutti i casi, è necessario usare Google Play Services. L'unico motivo per usare il pacchetto (Froyo) è la destinazione attiva di Froyo. L'unico motivo per cui questo file con estensione jar separato esiste da Google è dato dal fatto che Froyo si trova in una piccola percentuale di dispositivi che hanno deciso di smettere di supportarlo, quindi il file con estensione jar è uno snapshot bloccato e non supportato di Google Play Services.

### <a name="note-about-gingerbread"></a>Nota su Gingerbread

Per impostazione predefinita, Gingerbread non dispone del supporto per i frammenti e, per questo motivo, alcune delle classi nell'associazione non saranno utilizzabili in un'app in fase di esecuzione in un dispositivo Gingerbread. Le classi come `MapFragment` non funzioneranno in Gingerbread ed è invece necessario usare la relativa variante di supporto `SupportMapFragment`. Spetta allo sviluppatore scoprire quale usare. Questa incompatibilità è indicata da Google nella documentazione Google Play Services.

### <a name="what-happens-to-the-old-componentsnugets"></a>Cosa accade ai componenti precedenti/NuGet?

Poiché non sono più necessari, i componenti/NuGet seguenti sono stati disabilitati/deselezionati:

- Google Play Services (Gingerbread)
- Google Play Services (JellyBean)
- Google Play Services (KitKat)

Il componente _Google Play Services (ICS)_ esistente è stato rinominato in _Google Play Services_ e verrà mantenuto aggiornato in futuro. Tutti i progetti che fanno riferimento a uno dei pacchetti disabilitati/disattivati devono essere aggiornati per utilizzarli.

I componenti disabilitati sono ancora disponibili e devono essere ripristinabili per i progetti a cui viene ancora fatto riferimento in, per evitare interruzioni. Analogamente, i pacchetti NuGet deelencati continuano a esistere e possono essere ripristinati. Non verranno aggiornati in futuro.
