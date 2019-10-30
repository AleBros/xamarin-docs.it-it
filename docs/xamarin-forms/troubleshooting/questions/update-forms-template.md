---
title: È possibile aggiornare il modello predefinito di Xamarin.Forms a un pacchetto NuGet più recente?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 160FBE13-26EB-4B4F-9248-A5CBE58FDD7F
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/25/2017
ms.openlocfilehash: 4a628deb3e6f9282d49d71ac694506c3a0616ee9
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2019
ms.locfileid: "73005386"
---
# <a name="can-i-update-the-xamarinforms-default-template-to-a-newer-nuget-package"></a>È possibile aggiornare il modello predefinito di Xamarin.Forms a un pacchetto NuGet più recente?

Questa guida usa il modello di libreria .NET Standard Novell. Forms come esempio, ma lo stesso metodo generale funzionerà anche per il modello di progetto condiviso Novell. Forms. Questa guida è stata scritta con l'esempio di aggiornamento da Novell. Forms 1.5.1.6471 a 2.1.0.6529, ma gli stessi passaggi sono possibili per impostare altre versioni come valore predefinito.

1. Copiare il modello originale `.zip` da:

    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\T\PT\Cross-Platform\Xamarin.Forms.PCL.zip`

2. Decomprimere il `.zip` in un percorso temporaneo.

3. Modificare tutte le occorrenze della versione precedente del pacchetto Novell. Forms nella nuova versione che si vuole usare.
    * `FormsTemplate\FormsTemplate.vstemplate`
    * `FormsTemplate.Android\FormsTemplate.Android.vstemplate`
    * `FormsTemplate.iOS\FormsTemplate.iOS.vstemplate`

    Esempio: `<package id="Xamarin.Forms" version="1.5.1.6471" />` -> `<package id="Xamarin.Forms" version="2.1.0.6529" />`

4. Modificare l'elemento "Name" del file del [modello](https://msdn.microsoft.com/library/ms185308.aspx) di più progetti principale (`Xamarin.Forms.PCL.vstemplate`) per renderlo univoco. Esempio:

    > `<Name>Blank App (Xamarin.Forms Portable) - 2.1.0.6529</Name>`

5. Eseguire nuovamente il CAP dell'intera cartella del modello. Assicurarsi di trovare la corrispondenza con la struttura di file originale del file di `.zip`. Il file di `Xamarin.Forms.PCL.vstemplate` deve trovarsi all'inizio del file di `.zip`, non all'interno di alcuna cartella.

6. Creare una sottodirectory "app per dispositivi mobili" nella cartella dei modelli di Visual Studio per ogni utente:
    > `%USERPROFILE%\Documents\Visual Studio 2013\Templates\ProjectTemplates\Visual C#\Mobile Apps`

7. Copiare la nuova cartella del modello compresso nella nuova directory "app per dispositivi mobili".

8. Scaricare il pacchetto NuGet che corrisponde alla versione del passaggio 3. Ad esempio, [https://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529](https://nuget.org/api/v2/package/Xamarin.Forms/2.1.0.6529) (vedere anche [https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file](https://stackoverflow.com/questions/8597375/how-to-get-the-url-of-a-nupkg-file)) e copiarlo nella sottocartella appropriata della cartella Novell di Visual Studio Extensions:
    > `C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Xamarin\Xamarin\[Xamarin Version]\Packages`
