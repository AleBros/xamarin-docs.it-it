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
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "73019533"
---
# <a name="smarter-xamarin-android-support-v4--v13-nuget-packages"></a>Miglioramenti per i pacchetti NuGet Xamarin.Android.Support v4 / v13

## <a name="about-the-android-support-libraries"></a>Informazioni sulle librerie di supporto Android

Google ha creato librerie di supporto per rendere disponibili nuove funzionalità per le versioni precedenti di Android. In generale, alle librerie di supporto viene assegnato un numero di versione nel nome, che è il livello di API Android più basso con cui sono compatibili (ad esempio: Support-v4 può essere utilizzato solo su API Level 4 e versioni successive. Ulteriori informazioni in questa [discussione Stack Overflow](https://stackoverflow.com/questions/9926403/android-support-package-compatibility-library-use-v4-or-v13)). 

Due delle librerie `Support-v4` `Support-v13` di supporto: e non possono essere utilizzate insieme nella stessa app, cioè si escludono a vicenda. Questo perché `Support-v13` contiene effettivamente tutti i `Support-v4`tipi e l'implementazione di . Se si tenta di fare riferimento a entrambi nello stesso progetto, si verificheranno errori di tipo duplicato.

## <a name="problems-with-referencing"></a>Problemi con i riferimenti

Da `Support-v4` allora è diventato così popolare, un sacco di librerie di terze parti ora dipendono da esso. Avrebbero potuto scegliere di dipendere da Support-v13, ma è più comune dipendere dalla _v4_ poiché ciò offre a tutte le app che usano queste librerie di terze parti la possibilità di supportare i livelli API fino a 4.

Se una libreria Xamarin di `Xamarin.Android.Support.v4.dll` terze `Support-v4`parti fa riferimento all'associazione a , anche qualsiasi app che utilizza questa libreria deve fare riferimento `Xamarin.Android.Support.v4.dll`a . Questo diventa un problema quando la stessa applicazione vuole `Xamarin.Android.Support.v13.dll` anche `Support-v13`utilizzare alcune delle funzionalità dall'associazione a . Se si fa riferimento a entrambe le associazioni, si verificheranno errori di tipo duplicato.

## <a name="type-forwarded-v4-binding-assembly"></a>Assembly di associazione v4 inoltrato dal tipoType-Forwarded v4 Binding Assembly

Per risolvere questo problema, è `Xamarin.Android.Support.v4.dll` stato creato un assembly `[assembly: TypeForwardedTo (..)]` speciale che non `Support-v4` ha alcuna implementazione, ma semplicemente attributi che inoltrano tutti i tipi all'implementazione all'interno dell'assembly. `Xamarin.Android.Support.v13.dll`

Ciò significa che uno sviluppatore può fare riferimento a questo assembly _inoltrato_ dal tipo nell'app che soddisferà il riferimento da `Xamarin.Android.Support.v4.dll` qualsiasi libreria di terze parti, pur consentendo `Xamarin.Android.Support.v13.dll` di essere usato nell'app.

## <a name="nuget-assistance"></a>Assistenza NuGet

Mentre uno sviluppatore potrebbe aggiungere manualmente i riferimenti corretti necessari, siamo in grado di utilizzare NuGet per scegliere l'assembly corretto (il normale associazione _v4_ o il tipo inoltrato _v4_ assembly) quando viene installato il pacchetto NuGet.

Così, `Xamarin.Android.Support.v4` il pacchetto NuGet ora contiene la logica seguente:

Se la tua app è destinata all'API level 13 (Gingerbread 3.2) o versione successiva:

* `Xamarin.Android.Support.v13`NuGet verrà aggiunto automaticamente come dipendenzaNuGet will automatically be added as a dependency
* Al _tipo inoltrato_ `Xamarin.Android.Support.v4.dll` verrà fatto riferimento nel progetto

Se la tua app ha una destinazione inferiore a livello `Xamarin.Android.Support.v4.dll` di API 13, otterrai l'associazione normale a cui viene fatto riferimento nel progetto.

## <a name="do-i-have-to-use-support-v13"></a>È necessario utilizzare Support-v13?

Se l'app è destinata all'API Level 13 `Xamarin Android Support-v4` o superiore e `Xamarin Android Support v13` si sceglie di usare il pacchetto NuGet, il pacchetto NuGet è una dipendenza obbligatoria.

Riteniamo che l'aumento molto minore delle dimensioni dell'app (i due file .jar differiscono per 17kb) vale la compatibilità e meno mal di testa si traduce in.

Se sei irremovibile sull'uso `Support-v4` in un'app destinata al livello `.nupkg`API 13 o superiore, puoi sempre scaricare manualmente , estrarlo e fare riferimento all'assembly.
