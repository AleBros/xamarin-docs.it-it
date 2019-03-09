---
title: Miglioramenti per i pacchetti NuGet Xamarin.Android.Support v4 / v13
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: a990d933c258812b2b3d3374fb6435af06f729ea
ms.sourcegitcommit: 57e8a0a10246ff9a4bd37f01d67ddc635f81e723
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2019
ms.locfileid: "57671793"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Miglioramenti per i pacchetti NuGet Xamarin.Android.Support v4 / v13

## <a name="about-the-android-support-libraries"></a>Sulle librerie di supporto per Android

Google ha creato le librerie di supporto per rendere le nuove funzionalità disponibili per le versioni precedenti di Android. In generale, le librerie di supporto viene assegnate un numero di versione nel nome, ovvero il livello API Android più basso sono compatibili con (ad esempio: Supporto-v4 è utilizzabile solo in API di livello 4 e versioni successive. Altre informazioni in questo [discussione su Stack Overflow](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Due delle librerie di supporto: `Support-v4` e `Support-v13` non possono essere utilizzate insieme nella stessa app, vale a dire si escludono a vicenda. Infatti `Support-v13` effettivamente contiene tutti i tipi e l'implementazione di `Support-v4`. Se si prova a fare riferimento a entrambi nello stesso progetto si verificheranno errori di tipo duplicati.

## <a name="problems-with-referencing"></a>Problemi relativi al riferimento

Poiché `Support-v4` è diventata estremamente diffuso, molte delle librerie di terze parti 3rd ora dipendono da esso. È stato scelto per dipendono invece dal supporto v13, ma è più comune per dipendono _v4_ poiché le app usando queste librerie di terze parti 3rd che offre la possibilità di supportare i livelli API fino a 4.

Se fa riferimento a una libreria di terze parti 3rd Xamarin il `Xamarin.Android.Support.v4.dll` associazione `Support-v4`, deve anche fare riferimento a qualsiasi app che usa questa libreria `Xamarin.Android.Support.v4.dll`. Questo diventa un problema quando la stessa app desidera inoltre alcune delle funzionalità da usare la `Xamarin.Android.Support.v13.dll` associazione a `Support-v13`. Se si fa riferimento a entrambe le associazioni, si verificheranno errori di tipo duplicati.

## <a name="type-forwarded-v4-binding-assembly"></a>Il tipo inoltrato v4 associazione Assembly

Per risolvere questo problema, è stata creata una speciale `Xamarin.Android.Support.v4.dll` assembly che non ha un'implementazione, ma semplicemente `[assembly: TypeForwardedTo (..)]` gli attributi che inoltrano tutte la `Support-v4` tipi per l'implementazione all'interno di `Xamarin.Android.Support.v13.dll` assembly.

Ciò significa che uno sviluppatore può fare riferimento a questo _inoltrati_ nell'app per le quali verrà soddisfatta il riferimento all'assembly `Xamarin.Android.Support.v4.dll` da eventuali librerie di terze parti 3rd, consentendo allo stesso tempo `Xamarin.Android.Support.v13.dll` da usare nell'app.

## <a name="nuget-assistance"></a>Assistenza per NuGet

Anche se uno sviluppatore è stato possibile aggiungere manualmente i necessari riferimenti corretti, siamo in grado di usare NuGet per informazioni su come scegliere l'assembly corretto (entrambi la normale _v4_ associazione o il tipo inoltrato _v4_ assembly) quando il pacchetto NuGet è installato.

Pertanto, il `Xamarin.Android.Support.v4` pacchetto NuGet contiene ora la logica seguente:

Se l'app è destinata a livello di API 13 (Gingerbread 3.2) o versione successiva:

*   `Xamarin.Android.Support.v13` NuGet verrà automaticamente aggiunto come dipendenza
*   Il _inoltrati_ `Xamarin.Android.Support.v4.dll` verrà fatto riferimento nel progetto

Se l'app è destinata a un valore minore di API a livello di 13, si otterrà la normale `Xamarin.Android.Support.v4.dll` binding cui viene fatto riferimento nel progetto.

## <a name="do-i-have-to-use-support-v13"></a>È necessario usare il supporto v13?

Se l'app è destinata a livello API 13 o successivo e si sceglie di usare la `Xamarin Android Support-v4` pacchetto NuGet, quindi il `Xamarin Android Support v13` pacchetto NuGet è una dipendenza richiesta.

Siamo lievi aumento delle dimensioni di app (i due file con estensione jar sono diversi per kb 17) è senz' la pena la compatibilità e mal di testa un minor numero che comporta.

Se sono irremovibile su usando `Support-v4` in un'app destinata a 13 a livello di API o versioni successive, è possibile scaricare sempre manualmente i `.nupkg`, estrarre i file e fare riferimento all'assembly.
