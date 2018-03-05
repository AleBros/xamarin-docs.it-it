---
title: Progetti di installazione Windows
description: Aggiunta di nuovi progetti di Windows a una soluzione xamarin. Forms esistente
ms.topic: article
ms.prod: xamarin
ms.assetid: A0774D2E-6994-4D91-84E8-DAB66FC92320
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 02/16/2016
ms.openlocfilehash: 2acabc68c595bfb3bbd5c3516f68cd777ba10327
ms.sourcegitcommit: 61f5ecc5a2b5dcfbefdef91664d7460c0ee2f357
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="setup-windows-projects"></a>Progetti di installazione Windows

_Aggiunta di nuovi progetti di Windows a una soluzione xamarin. Forms esistente_

I progetti più vecchi di xamarin. Forms (o quelle create in un sistema operativo Mac&nbsp;X) non avrà questi configurazione di progetti di Windows.

Ciò significa che è necessario aggiungere manualmente questi tipi di progetto per compilare le app di Windows 8.1, Windows Phone 8.1 e Windows 10 (UWP).

## <a name="add-a-windows-81-app"></a>Aggiungere un'app di Windows 8.1

* Se è stato utilizzato il modello, libreria di classi Portabile [aggiornare il profilo](#pcl), quindi
* [Aggiungere un'app di Windows 8.1](~/xamarin-forms/platform/windows/installation/tablet.md) per fattori di forma tablet/desktop.

## <a name="add-a-windows-phone-81-app"></a>Aggiungere un Windows Phone 8.1 app

* Se è stato utilizzato il modello, libreria di classi Portabile [aggiornare il profilo](#pcl), quindi
* [Aggiungere un Windows Phone 8.1 app](~/xamarin-forms/platform/windows/installation/phone.md)

## <a name="add-a-universal-windows-platform-uwp-app"></a>Aggiungere un Universal Windows Platform (UWP) app

* Compilazione di [UWP](https://msdn.microsoft.com/library/windows/apps/dn894631.aspx) App richiede Visual Studio 2015, in esecuzione in Windows 10.
* Se è stato utilizzato il modello, libreria di classi Portabile [aggiornare il profilo](#pcl), quindi
* [Aggiungere un Windows Universal app della piattaforma](~/xamarin-forms/platform/windows/installation/universal.md)

<a name="pcl" />

### <a name="update-your-pcl-profile"></a>Aggiornare il profilo della libreria di classi Portabile

Se l'app xamarin. Forms esistente utilizzato il modello libreria di classe portabile (PCL), è necessario aggiornare il profilo.

1. **Fare doppio clic su > proprietà** (le impostazioni esistenti potrebbero essere diverso)

  ![](images/targets.png "Destinazioni di libreria di classi Portabile")

2. Fare clic su di **modifica...**  pulsante

3. Verificare che il **Windows 8** e **Windows Phone 8.1** le opzioni sono selezionate (e **Windows Phone Silveright** è *deselezionate*):

  ![](images/pcl.png "Opzioni di destinazione di libreria di classi Portabile")

4. Premere **OK** e salvare le modifiche.

Ciò corrisponde a **111 profilo** se si configura la libreria di classi Portabile in Visual Studio per Mac utilizzando l'elenco a discesa.

  ![](images/pcl-xs.png "Profilo PCL 111")

**Nota:** se la soluzione dispone ancora di un progetto Windows Phone 8 Silverlight, libreria di classi Portabile deve essere impostato su profilo 259. Il supporto per Windows Phone 8 Silverlight è deprecato, pertanto si consiglia di sostituirlo con i tipi di progetto riportati in questa pagina.
