---
title: Perché non è accedere a Xamarin in Visual Studio o Visual Studio per Mac?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6EF2B553-5DF9-41CC-B68F-77A7F64572FA
ms.technology: xamarin-cross-platform
author: asb3993
ms.author: amburns
ms.openlocfilehash: cb750a7c282ecab6e2193bb554e470086868e018
ms.sourcegitcommit: 6f7033a598407b3e77914a85a3f650544a4b6339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/07/2018
---
# <a name="why-cant-i-log-into-xamarin-in-visual-studio-or-visual-studio-for-mac"></a>Perché non è accedere a Xamarin in Visual Studio o Visual Studio per Mac?

> [!IMPORTANT]
> Questa Guida non è supportata per la maggior parte degli utenti MSDN poiché non sono necessari per il proprietario o di log in account Xamarin non usa il [memorizzare i componenti di Xamarin](https://components.xamarin.com/) o [Visual Studio per Mac (Mac)](~/cross-platform/get-started/requirements.md). I titolari di licenza MSDN devono fare riferimento a questo [Guida alle opzioni di licenza](~/cross-platform/get-started/requirements.md) invece.



## <a name="overview"></a>Panoramica
Esistono alcune cause comuni che potrebbero impedire l'accesso al tuo account Xamarin nell'IDE. Problemi noti e le correzioni sono descritti di seguito.

### <a name="finding-the-login-screen"></a>Ricerca la schermata di accesso

Per riferimento, le schermate di accesso sono disponibili qui:

- Visual Studio per Mac
   - Angolo superiore destro della schermata iniziale
   - **Visual Studio per Mac > Account** (Mac)
   - **Strumenti > Account** (Windows)
- Visual Studio
   - **Strumenti > Account Xamarin**

## <a name="the-ide-is-connecting-but-the-account-screen-isnt-showing-correct-login-information"></a>L'IDE si connette, ma la schermata di account non viene visualizzato informazioni di accesso corretto

Viene in genere risolto con una risincronizzazione licenza manuale.
Seguire le istruzioni fornite in questo articolo: [ricerca per categorie manualmente le licenze Xamarin resyncronize?](~/cross-platform/troubleshooting/legacy-licenses/resync-licenses.md)

## <a name="invalid-account-information"></a>Informazioni sull'Account non valido

Se si passa al sito Web Xamarin [pagina di accesso](https://store.xamarin.com/Login?from=%2faccount%2f), è possibile provare ad accedere con le credenziali dell'account corrente.
La pagina contiene inoltre i collegamenti per reimpostare la password e per creare un nuovo account.

## <a name="account-is-valid-but-the-ide-cant-connect"></a>Account sia valido, ma l'IDE non è possibile connettersi

Ciò è dovuto in genere quando si bloccano l'IDE di accedere a endpoint necessari firewall o altre impostazioni di sicurezza.
I server di attivazione devono accedere alle seguenti:

> Activation.xamarin.com store.xamarin.com auth.xamarin.com

Tuttavia, diversi altri endpoint sono importanti per i processi di sviluppo generale, ad esempio gli aggiornamenti, il recupero di pacchetti NuGet e così via. Per questo motivo, è consigliabile per verificare *tutti* degli endpoint vengono aggiunti dal [Guida alla configurazione di firewall Xamarin](~/cross-platform/get-started/installation/firewall.md).

### <a name="ios-in-xamarin-studio-windows"></a>iOS in Xamarin Studio Windows
lo sviluppo iOS non è supportato in Xamarin Studio per Windows. Quando si accede alla schermata di accesso qui, non verrà visualizzata la licenza di iOS.

In alternativa, account di accesso tramite Xamarin Studio (Mac) o Visual Studio con una licenza legacy. Si noti che gli utenti MSDN in Windows non è necessario effettuare l'accesso.

## <a name="additional-support"></a>Supporto aggiuntivo

Se gli scenari sopra non descrivere la situazione o risolvere il problema, fare riferimento a queste [opzioni di supporto](https://www.xamarin.com/support).
