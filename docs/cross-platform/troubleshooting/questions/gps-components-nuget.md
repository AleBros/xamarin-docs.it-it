---
title: Unificazione dei componenti di Google Play Services e NuGet
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 5D962EB4-2CB3-4B7D-9D77-889DEACDAE02
author: asb3993
ms.author: amburns
ms.date: 05/08/2018
ms.openlocfilehash: 3f5c5f75ae1c7a44537afa59ff4a15d54b1df50b
ms.sourcegitcommit: 4b402d1c508fa84e4fc3171a6e43b811323948fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61357436"
---
# <a name="unifying-google-play-services-components-and-nuget"></a>Unificazione dei componenti di Google Play Services e NuGet

## <a name="history"></a>Cronologia

In passato diversi componenti di Google Play Services e pacchetti NuGet:

-   Google Play Services (Froyo)
-   Google Play Services (Gingerbread)
-   Google Play Services (ICS)
-   Google Play Services (JellyBean)
-   Google Play Services (KitKat)

Google. jar fornito due effettivamente solo i file da Google Play Services:

-   `google-play-services-froyo.jar`
-   `google-play-services.jar`

La discrepanza esisteva perché i nostri strumenti correttamente diceva `aapt.exe` qual è la risorsa di massima livello di API utilizzabili per una determinata app. In questo modo, è pervenuta errori di compilazione se abbiamo provato a usare l'associazione di Google Play Services (KitKat) su un livello API inferiore, ad esempio Gingerbread.

## <a name="unifying-google-play-services"></a>Unificando le funzionalità di Google Play Services

Nelle versioni più recenti di xamarin. Android, è ora possibile indicare `aapt.exe` versione massima di risorse da usare, in modo che questo problema si risolve per noi.

Ciò significa che non è affatto reale avere pacchetti separati per Gingerbread/ICS/JellyBean/KitKat (tuttavia è comunque necessario un binding distinto per Froyo perché è del tutto un file con estensione jar diverso).

Per semplificare le cose per gli sviluppatori, è stata unificata ora i componenti e NuGet pacchetti in due parti:

-   Google Play Services (Froyo) (associa `google-play-services-froyo.jar`)
-   Google Play Services (associa `google-play-services.jar`)

### <a name="which-one-should-be-used"></a>Quale usare?

In quasi ogni caso, usare Google Play Services. L'unico motivo per usare il pacchetto (Froyo) è se si usa attivamente Froyo. L'unico motivo per cui che esiste questo file separato con estensione jar da Google poiché Froyo si trova su una piccola percentuale di dispositivi, vengono anch'essi hanno deciso di interrompere il supporto, in modo che questo file con estensione jar è uno snapshot bloccato, non supportato di Google Play Services.

### <a name="note-about-gingerbread"></a>Nota sugli Gingerbread

Gingerbread è privo di frammento supportano per impostazione predefinita e per questo motivo, alcune delle classi di associazione non sarà utilizzabile in un'app in fase di esecuzione in un dispositivo Gingerbread. Le classi come `MapFragment` non funzionerà nei Gingerbread e usare invece la variante supporto `SupportMapFragment`. Spetta allo sviluppatore di sapere quale usare. Questa incompatibilità è indicata da Google nella relativa documentazione di Google Play Services.

### <a name="what-happens-to-the-old-componentsnugets"></a>Cosa accade ai vecchi componenti/di NuGet?

Dato che non sono più necessari, è necessario disabilitato/Delisted componenti/pacchetti NuGet seguenti:

-   Google Play Services (Gingerbread)
-   Google Play Services (JellyBean)
-   Google Play Services (KitKat)

L'oggetto esistente _Google Play Services (ICS)_ componente/Nuget è stato rinominato in _Google Play Services_ e verranno mantenute aggiornate in futuro. Tutti i progetti che fanno riferimento a uno dei pacchetti disabilitate/Delisted devono essere aggiornati per usare questo.

I componenti disabilitati ancora esistano e devono essere ripristinabili per i progetti che vi fanno ancora riferimento, per evitare l'interruzione li. In modo analogo i pacchetti NuGet delisted ancora presenti e possono essere ripristinati. Essi non verrà aggiornati in futuro.
