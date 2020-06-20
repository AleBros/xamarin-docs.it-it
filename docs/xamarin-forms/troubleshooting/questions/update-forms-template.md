---
title: È possibile aggiornare il Xamarin.Forms modello predefinito a un pacchetto NuGet più recente?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: bdead80671a1ae6539de6614441df7e86863a5a6
ms.sourcegitcommit: 32d2476a5f9016baa231b7471c88c1d4ccc08eb8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2020
ms.locfileid: "84137475"
---
# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>È possibile aggiornare il Xamarin.Forms modello predefinito a un pacchetto NuGet più recente?

Questa guida usa il Xamarin.Forms modello di libreria .NET standard come esempio, ma lo stesso metodo generale funzionerà anche per il Xamarin.Forms modello di progetto condiviso. Questa guida è stata scritta con l'esempio di aggiornamento da Xamarin.Forms 1.5.1.6471 a 2.1.0.6529, ma gli stessi passaggi sono possibili per impostare altre versioni come valore predefinito.

1. Copiare il modello originale `.zip` da:

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2. Decomprimere il `.zip` in un percorso temporaneo.

3. Modificare tutte le occorrenze della versione precedente del Xamarin.Forms pacchetto con la nuova versione che si desidera utilizzare.
    * `FormsTemplate\FormsTemplate.vstemplate`
    * `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    * `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    Esempio: `<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4. Modificare l'elemento "Name" del file del [modello](https://msdn.microsoft.com/library/ms185308.aspx) di più progetti principale ( `Xamarin.Forms.PCL.vstemplate` ) per renderlo univoco. Ad esempio:

    > `<Name>Blank App (Xamarin.Forms Portable) - 2.1.0.6529</Name>`

5. Eseguire nuovamente il CAP dell'intera cartella del modello. Assicurarsi di trovare la corrispondenza con la struttura di file originale del `.zip` file. Il `Xamarin.Forms.PCL.vstemplate` file deve trovarsi all'inizio del `.zip` file, non all'interno di alcuna cartella.

6. Creare una sottodirectory "app per dispositivi mobili" nella cartella dei modelli di Visual Studio per ogni utente:
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7. Copiare la nuova cartella del modello compresso nella nuova directory "app per dispositivi mobili".

8. Scaricare il pacchetto NuGet che corrisponde alla versione del passaggio 3. Ad esempio, [ https://nuget.org/api/v2/package/ Xamarin.Forms /2.1.0.6529](https://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (vedere anche [https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file](https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file) ) e copiarlo nella sottocartella appropriata della cartella Novell di Visual Studio Extensions:
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
