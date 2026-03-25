# FB Friends Cleanup Script 🗑️

Script de automatización para la consola del navegador diseñado para eliminar amigos de Facebook de forma masiva, respetando una **lista blanca** (whitelist) de personas que deseas conservar.

---

## 🛠️ Instrucciones de Uso

1. **Entra a tu lista de amigos:** [https://www.facebook.com/friends/list](https://www.facebook.com/friends/list).
2. **Abre la consola de desarrollador:** * Windows/Linux: `Ctrl + Mayús + I` o `F12`.
   * Mac: `Cmd + Opt + J`.
3. **Pega el script completo** (en la sección de abajo) y presiona **Enter**.

---

## 🛡️ Lista Blanca (Nombres Protegidos)

El script no eliminará a ninguna persona cuyo nombre coincida exactamente con los que están en el array `names`. Actualmente, la lista protege a:

* **Contactos cercanos:** Luna Itzel, Andrés Hernández, Mónica, Alondra Ruiz, etc.
* **Personalización:** Si quieres conservar a alguien más, agrega su nombre entre comillas simples dentro del array `names` al inicio del código.

---

## ⚙️ Cómo Funciona

* **Validación inteligente:** Compara el nombre del perfil con la lista blanca antes de ejecutar cualquier acción.
* **Manejo de Errores:** Utiliza un bloque `try-catch` para que, si un elemento no carga a tiempo o un clic falla, el script continúe con el siguiente amigo sin detenerse.
* **Ciclo de Carga:** Debido a que Facebook solo carga aproximadamente 20 amigos a la vez, el script realiza una recarga automática (`window.location.reload()`) al finalizar la tanda detectada.
* **Delays de Seguridad:** Incluye esperas de 500ms para asegurar que los menús contextuales se rendericen correctamente.

---

## ⚠️ Advertencias y Riesgos

> [!CAUTION]
> 1. **Clases Dinámicas:** Facebook actualiza sus clases de CSS (ej. `x193iq5w...`) frecuentemente. Si el script deja de detectar elementos, es necesario actualizar los selectores.
> 2. **Detección de Spam:** El uso excesivo de automatización puede alertar a los sistemas de seguridad de Facebook. Úsalo con moderación para evitar bloqueos temporales.

---

## 💻 El Código

```javascript
const names = [
    'Luna Itzel','Andrés Hernández','Mónica','Alondra Ruiz', 'Javier Santiago Gt', 
    'Andres Garcia', 'Ana Luissa GCruz', 'Anna Ferrer','Josue Eduardo Ramirez Bautista', 
    'Joanna Martinez', 'Paty Hernandez', 'Anely Maranto', 'Oziel Ledezma',
    'Selena Del Angel', 'Alexa Jiménez', 'Joaquin De La Cruz'
];

var btns = [...document.querySelectorAll('[role="button"][aria-label="Más"]')]; 
var etqs = [...document.querySelectorAll('span[class="x193iq5w xeuugli x13faqbe x1vvkbs x1xmvt09 x6prxxf xvq8zen x1s688f xzsf02u"]')];
var etqsText = etqs.map(etq => etq.textContent);

async function cleanFriends() {
    for (let i = 0; i < btns.length; i++) {
        if (!names.includes(etqsText[i])) {
            try {
                // Abrir menú de opciones
                btns[i].click();
                await new Promise(resolve => setTimeout(resolve, 500));
                
                // Seleccionar "Eliminar de mis amigos" (índice 3 por defecto)
                let menuItems = [...document.querySelectorAll('[role="menuitem"]')];
                if (menuItems[menuItems.length - 1].textContent.includes("Eliminar")) {
                    menuItems[menuItems.length - 1].click();
                    await new Promise(resolve => setTimeout(resolve, 500));
                    
                    // Confirmar eliminación
                    let okBtn = [...document.querySelectorAll('[role="button"][aria-label="Confirmar"]')];
                    if (okBtn[0]) {
                        okBtn[0].click();
                        console.log(`✅ Eliminado: ${etqsText[i]}`);
                        await new Promise(resolve => setTimeout(resolve, 500));
                    }
                }
            } catch (e) {
                console.error(`❌ Error con: ${etqsText[i]}`, e);
            }
        } else {
            console.log(`🛡️ Protegido: ${etqsText[i]}`);
        }
    }
    console.log("Tanda terminada. Recargando...");
    window.location.reload();
}

cleanFriends();
