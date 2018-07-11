---
ms.topic: include
ms.openlocfilehash: 5c11c94956f8d56c66c50a9a480177c5b77c2643
ms.sourcegitcommit: 632955f8cdb80712abd8dcc30e046cb9c435b922
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/10/2018
ms.locfileid: "37947434"
---
## <a name="sensor-speedxrefxamarinessentialssensorspeed"></a>[Velocità di sensore](xref:Xamarin.Essentials.SensorSpeed)

- **Più veloce** : ottenere i dati dei sensori più velocemente possibile (non garantisce la restituzione nel thread dell'interfaccia utente).
- **Gioco** – frequenza adatta per i giochi (non garantite la restituzione nel thread dell'interfaccia utente).
- **Normale** – frequenza predefinita adatta per le modifiche dell'orientamento dello schermo.
- **Interfaccia utente** – frequenza appropriata per l'interfaccia utente generale.

Se il gestore eventi non è garantito a essere eseguite sul thread dell'interfaccia utente e se il gestore eventi deve accedere a elementi dell'interfaccia utente, usare il [ `MainThread.BeginInvokeOnMainThread` ](~/essentials/main-thread.md) metodo per eseguire il codice sul thread UI.