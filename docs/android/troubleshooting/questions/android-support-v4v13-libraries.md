---
title: Miglioramenti per i pacchetti NuGet Xamarin.Android.Support v4 / v13
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: c74cac6a6d669385855999a565711a3fdc85f3b7
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73019533"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Miglioramenti per i pacchetti NuGet Xamarin.Android.Support v4 / v13

## <a name="about-the-android-support-libraries"></a>Informazioni sulle librerie di supporto Android

Google ha creato librerie di supporto per rendere disponibili nuove funzionalità per le versioni precedenti di Android. In generale, alle librerie di supporto viene assegnato un numero di versione nel nome, ovvero il livello di API Android più basso con cui sono compatibili (ad esempio, il supporto-V4 può essere usato solo sul livello API 4 e versioni successive. Altre informazioni in questa [stack overflow discussione](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Due delle librerie di supporto: `Support-v4` e `Support-v13` non possono essere usate insieme nella stessa app, ovvero si escludono a vicenda. Questo perché `Support-v13` contiene effettivamente tutti i tipi e l'implementazione di `Support-v4`. Se si tenta di fare riferimento a entrambi nello stesso progetto, si verificheranno errori di tipo duplicati.

## <a name="problems-with-referencing"></a>Problemi relativi al riferimento

Poiché `Support-v4` è diventato così popolare, numerose librerie di terze parti ora dipendono da esso. Potrebbero avere scelto di dipendere dal supporto-V13, ma è più comune dipendere da _V4_ , perché fornisce alle app che usano queste librerie di terze parti la possibilità di supportare i livelli API fino a 4.

Se una libreria di terze parti Xamarin fa riferimento al binding `Xamarin.Android.Support.v4.dll` `Support-v4`, anche qualsiasi app che usa questa libreria deve fare riferimento a `Xamarin.Android.Support.v4.dll`. Questo diventa un problema quando la stessa app vuole anche usare alcune funzionalità del binding `Xamarin.Android.Support.v13.dll` per `Support-v13`. Se si fa riferimento a entrambe le associazioni, si verificheranno errori di tipo duplicati.

## <a name="type-forwarded-v4-binding-assembly"></a>Assembly di binding V4 con inoltri di tipo

Per aggirare questo problema, è stato creato un assembly di `Xamarin.Android.Support.v4.dll` speciale che non dispone di implementazione, ma è sufficiente `[assembly: TypeForwardedTo (..)]` attributi che inoltrino tutti i tipi di `Support-v4` all'implementazione all'interno dell'assembly `Xamarin.Android.Support.v13.dll`.

Ciò significa che uno sviluppatore può fare riferimento a questo assembly con _inoltri_ in base al tipo nell'app che soddisferà il riferimento a `Xamarin.Android.Support.v4.dll` da qualsiasi libreria di terze parti, consentendo allo stesso tempo di usare `Xamarin.Android.Support.v13.dll` nell'app.

## <a name="nuget-assistance"></a>Assistenza NuGet

Sebbene uno sviluppatore possa aggiungere manualmente i riferimenti corretti, è possibile usare NuGet per scegliere l'assembly corretto (il binding _V4_ normale o l'assembly _V4_ con inoltri al tipo) quando viene installato il pacchetto NuGet.

Il pacchetto NuGet `Xamarin.Android.Support.v4` ora contiene la logica seguente:

Se l'app è destinata a livello API 13 (Gingerbread 3,2) o superiore:

* `Xamarin.Android.Support.v13` NuGet verrà automaticamente aggiunto come dipendenza
* Il progetto verrà usato come riferimento all'`Xamarin.Android.Support.v4.dll` con _inoltri al tipo_

Se l'app è destinata a un valore inferiore al livello API 13, si otterrà il normale binding di `Xamarin.Android.Support.v4.dll` a cui si fa riferimento nel progetto.

## <a name="do-i-have-to-use-support-v13"></a>È necessario usare il supporto-V13?

Se l'app è destinata al livello API 13 o superiore e si sceglie di usare il pacchetto NuGet `Xamarin Android Support-v4`, il pacchetto NuGet `Xamarin Android Support v13` è una dipendenza obbligatoria.

L'aumento delle dimensioni dell'app è molto minore (i due file con estensione jar sono diversi da 17kb), vale la pena la compatibilità e il minor numero di cefalee che produce.

Se si intende usare `Support-v4` in un'app destinata a livello API 13 o superiore, è sempre possibile scaricare manualmente il `.nupkg`, estrarlo e fare riferimento all'assembly.
