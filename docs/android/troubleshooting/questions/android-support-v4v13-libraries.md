---
title: Miglioramenti per i pacchetti NuGet Xamarin.Android.Support v4 / v13
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: FE66A82A-6C05-4646-BC52-E806F5DC606C
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: 64ceacc37a303e69b0dd7073ec6547ed344af51d
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69523438"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Miglioramenti per i pacchetti NuGet Xamarin.Android.Support v4 / v13

## <a name="about-the-android-support-libraries"></a>Informazioni sulle librerie di supporto Android

Google ha creato librerie di supporto per rendere disponibili nuove funzionalità per le versioni precedenti di Android. In generale, alle librerie di supporto viene assegnato un numero di versione nel nome, ovvero il livello di API Android più basso con cui sono compatibili (ad esempio: Supporto: V4 può essere usato solo nel livello API 4 e versioni successive. Altre informazioni in questa [stack overflow discussione](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Due delle librerie di supporto: `Support-v4` e `Support-v13` non possono essere usate insieme nella stessa app, ovvero si escludono a vicenda. Questo perché `Support-v13` contiene effettivamente tutti i tipi e l'implementazione di `Support-v4`. Se si tenta di fare riferimento a entrambi nello stesso progetto, si verificheranno errori di tipo duplicati.

## <a name="problems-with-referencing"></a>Problemi relativi al riferimento

Poiché `Support-v4` è diventato così popolare, numerose librerie di terze parti ora dipendono da esso. Potrebbero avere scelto di dipendere dal supporto-V13, ma è più comune dipendere da _V4_ , perché fornisce alle app che usano queste librerie di terze parti la possibilità di supportare i livelli API fino a 4.

Se una libreria di terze parti Novell `Xamarin.Android.Support.v4.dll` fa riferimento `Support-v4`al binding a, è necessario fare riferimento `Xamarin.Android.Support.v4.dll`anche a qualsiasi app che usa questa libreria. Questo diventa un problema quando la stessa app vuole anche usare alcune delle funzionalità `Xamarin.Android.Support.v13.dll` dell'associazione a. `Support-v13` Se si fa riferimento a entrambe le associazioni, si verificheranno errori di tipo duplicati.

## <a name="type-forwarded-v4-binding-assembly"></a>Assembly di binding V4 con inoltri di tipo

Per aggirare questo problema, è stato creato un assembly `Xamarin.Android.Support.v4.dll` speciale che non dispone di implementazione, ma `[assembly: TypeForwardedTo (..)]` semplicemente gli attributi che inviano `Support-v4` tutti i tipi all'implementazione all' `Xamarin.Android.Support.v13.dll` interno dell'assembly.

Ciò significa che uno sviluppatore può fare riferimento a questo assembly con inoltri in base al _tipo_ nell'app che `Xamarin.Android.Support.v4.dll` soddisferà il riferimento a da qualsiasi libreria di `Xamarin.Android.Support.v13.dll` terze parti, consentendo comunque l'uso nell'app.

## <a name="nuget-assistance"></a>Assistenza NuGet

Sebbene uno sviluppatore possa aggiungere manualmente i riferimenti corretti, è possibile usare NuGet per scegliere l'assembly corretto (il binding _V4_ normale o l'assembly _V4_ con inoltri al tipo) quando viene installato il pacchetto NuGet.

Il `Xamarin.Android.Support.v4` pacchetto NuGet ora contiene la logica seguente:

Se l'app è destinata a livello API 13 (Gingerbread 3,2) o superiore:

* `Xamarin.Android.Support.v13`NuGet verrà automaticamente aggiunto come dipendenza
* Il `Xamarin.Android.Support.v4.dll` progetto verrà usato come riferimento per il tipo.

Se l'app è destinata a un valore inferiore al livello API 13, si otterrà l' `Xamarin.Android.Support.v4.dll` Associazione normale a cui si fa riferimento nel progetto.

## <a name="do-i-have-to-use-support-v13"></a>È necessario usare il supporto-V13?

Se l'app è destinata al livello API 13 o superiore e si sceglie di usare `Xamarin Android Support-v4` il pacchetto NuGet, il `Xamarin Android Support v13` pacchetto NuGet è una dipendenza obbligatoria.

L'aumento delle dimensioni dell'app è molto minore (i due file con estensione jar sono diversi da 17kb), vale la pena la compatibilità e il minor numero di cefalee che produce.

Se si intende usare `Support-v4` in un'app destinata a livello API 13 o superiore, è sempre possibile `.nupkg`scaricare manualmente, estrarlo e fare riferimento all'assembly.
