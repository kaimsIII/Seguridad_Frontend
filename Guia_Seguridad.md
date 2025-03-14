# Manual de Seguridad para Frontend

## 🔒 Introducción
Este manual contiene reglas y buenas prácticas de seguridad para el equipo de frontend, basadas en OWASP. Sigue estas recomendaciones para evitar vulnerabilidades en el navegador.

---

## 1. Validaciones en el Frontend
### ✅ Lo que debes hacer:
- Usa **HTML5** para validar formularios (`required`, `pattern`, `maxlength`).
- Aplica validaciones con **JavaScript** antes de enviar datos.
- Usa expresiones regulares (`regex`) para formatos de entrada.

### ❌ Lo que NO debes hacer:
- Confiar solo en las validaciones del frontend (el backend también debe validar).
- Usar `alert()` para mostrar errores (usa mensajes en pantalla).

**Ejemplo seguro:**
```html
<input type="email" id="email" required pattern="[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,}$">
```

```js
document.querySelector("form").addEventListener("submit", function (e) {
    let email = document.querySelector("#email").value;
    let regex = /^[a-z0-9._%+-]+@[a-z0-9.-]+\.[a-z]{2,}$/;
    
    if (!regex.test(email)) {
        alert("Correo inválido");
        e.preventDefault();
    }
});
```

---

## 2. Evitar Inyecciones de Código
### ✅ Lo que debes hacer:
- Usa `textContent` en lugar de `innerHTML` para mostrar datos.
- Evita `eval()` y `setTimeout("código", tiempo)`.

### ❌ Lo que NO debes hacer:
```js
let userInput = "alert('Hackeado!')";
eval(userInput); // ⚠️ PELIGROSO
```

**Alternativa segura:**
```js
document.getElementById("salida").textContent = userInput;
```

---

## 3. No Almacenes Datos Sensibles en el Navegador
### ✅ Lo que debes hacer:
- Usa `sessionStorage` en vez de `localStorage` si es necesario.
- Prefiere cookies con `HttpOnly` (esto lo maneja el backend).

### ❌ Lo que NO debes hacer:
```js
localStorage.setItem("token", "abc123"); // ⚠️ Peligroso
```

---

## 4. Evita Cargar Recursos HTTP
### ✅ Lo que debes hacer:
- Usa siempre HTTPS.
- Verifica que todos los recursos externos (scripts, fuentes, APIs) usen HTTPS.

**Ejemplo correcto:**
```html
<script src="https://example.com/script.js"></script>
```

**Evitar:**
```html
<script src="http://example.com/script.js"></script> <!-- ⚠️ Peligroso -->
```

---

## 5. Control de Permisos
### ✅ Lo que debes hacer:
- Usa `disabled` en botones o inputs si el usuario no tiene permiso.
- Controla permisos desde el backend y no solo con CSS.

```js
document.querySelector("#boton").disabled = true;
```

**❌ Lo que NO debes hacer:**
```css
button { display: none; } /* ⚠️ Se puede reactivar fácilmente con la consola del navegador */
```

---

## 6. Evitar Ataques de Fuerza Bruta en Login
### ✅ Lo que debes hacer:
- Retrasar intentos de login después de varios fallos.
- Implementar un CAPTCHA si hay intentos repetidos.

```js
let intentos = 0;
document.querySelector("form").addEventListener("submit", function (e) {
    intentos++;
    if (intentos > 3) {
        alert("Demasiados intentos. Espera 30 segundos.");
        e.preventDefault();
        setTimeout(() => (intentos = 0), 30000);
    }
});
```

---

## 7. Manejo Seguro de Errores
### ✅ Lo que debes hacer:
- Usa `try...catch` para manejar errores.
- Muestra mensajes genéricos al usuario.

```js
try {
    let respuesta = JSON.parse("{mal json}");
} catch (error) {
    console.error("Error procesando los datos", error);
    alert("Ocurrió un error. Intenta de nuevo.");
}
```


