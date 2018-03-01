---
title: Prontezza Xamarin Android supportano v4 / pacchetti NuGet v13
ms.topic: article
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: mgmclemore
ms.author: mamcle
ms.openlocfilehash: ab5ec19498b3c61e988adc959e1751b96f60210d
ms.sourcegitcommit: 6cd40d190abe38edd50fc74331be15324a845a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/27/2018
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Prontezza Xamarin Android supportano v4 / pacchetti NuGet v13

## <a name="about-the-android-support-libraries"></a>Informazioni sulle raccolte di supporto Android

Google ha creato le raccolte di supporto per rendere disponibile per le versioni precedenti di Android nuove funzionalità. In generale, le raccolte di supporto viene assegnate un numero di versione nel nome, ovvero il livello API Android più basso che siano compatibili con (ad esempio: supporto v4 può essere utilizzato solo su API livello 4 e versioni successive. Altre informazioni in questo [Overflow dello Stack discussione](http://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Due delle librerie di supporto: `Support-v4` e `Support-v13` non possono essere utilizzate insieme nella stessa applicazione, vale a dire si escludono a vicenda. In questo modo `Support-v13` effettivamente contiene tutti i tipi e l'implementazione di `Support-v4`. Se si prova e fare riferimento sia nello stesso progetto si verificheranno errori di tipo duplicato.

## <a name="problems-with-referencing"></a>Problemi relativi al riferimento

Poiché `Support-v4` è diventato così diffuso, molte delle librerie di terze parti 3rd ora dipendono da esso. Impossibile scelto invece dipendono dal supporto v13, ma è più comune per dipendono _v4_ poiché l'opzione di supportare fino a 4 livelli di API che consente di qualsiasi app di utilizzo di queste librerie di terze parti 3rd.

Se fa riferimento a una raccolta di parti 3rd Xamarin il `Xamarin.Android.Support.v4.dll` associazione a `Support-v4`, è necessario anche fare riferimento a qualsiasi app che usa questa libreria `Xamarin.Android.Support.v4.dll`. Questo diventa un problema quando la stessa applicazione desidera inoltre utilizzare alcune delle funzionalità dal `Xamarin.Android.Support.v13.dll` associazione `Support-v13`. Se si fa riferimento a entrambe le associazioni, si verificheranno errori di tipo duplicato.

## <a name="type-forwarded-v4-binding-assembly"></a>Tipo inoltrato v4 associazione Assembly

Per evitare questo problema, è stata creata una speciale `Xamarin.Android.Support.v4.dll` assembly che non contiene alcuna implementazione, ma semplicemente `[assembly: TypeForwardedTo (..)]` inoltrare tutti gli attributi il `Support-v4` tipi per l'implementazione all'interno di `Xamarin.Android.Support.v13.dll` assembly.

Ciò significa che uno sviluppatore può fare riferimento a questo _tipo inoltrato_ nell'applicazione che soddisfi il riferimento all'assembly `Xamarin.Android.Support.v4.dll` da tutte le librerie di terze parti 3rd, pur consentendo `Xamarin.Android.Support.v13.dll` da usare nell'app.

## <a name="nuget-assistance"></a>Assistenza di NuGet

Quando uno sviluppatore può aggiungere manualmente i necessari riferimenti corretti, siamo in grado di usare NuGet per scegliere l'assembly corretto (entrambi normale _v4_ associazione o il tipo inoltrato _v4_ assembly) quando il pacchetto NuGet è installato.

In tal caso, il `Xamarin.Android.Support.v4` pacchetto NuGet ora contiene la logica seguente:

Se l'app è destinato a livello di API 13 (pupazzi 3.2) o versione successiva:

*   `Xamarin.Android.Support.v13` NuGet verrà automaticamente aggiunto come una dipendenza
*   Il _tipo inoltrato_ `Xamarin.Android.Support.v4.dll` verrà fatto riferimento nel progetto

Se l'app è destinato a un valore inferiore a 13 livello API, si otterrà la normale `Xamarin.Android.Support.v4.dll` associazione a cui fa riferimento nel progetto.

## <a name="do-i-have-to-use-support-v13"></a>È necessario utilizzare il supporto v13?

Se l'app è destinato a livello di API 13 o versione successiva e si sceglie di utilizzare il `Xamarin Android Support-v4` pacchetto NuGet, quindi il `Xamarin Android Support v13` pacchetto NuGet è una dipendenza richiesta.

Riteniamo lievi aumento delle dimensioni di app (i due file JAR differiscono per 17kb) è importante la compatibilità e meno problemi che comporta.

Se si è irremovibile sull'utilizzo di `Support-v4` in un'applicazione destinata a 13 livello API o versioni successive, è possibile scaricare manualmente il `.nupkg`, decomprimerlo e fare riferimento all'assembly.
