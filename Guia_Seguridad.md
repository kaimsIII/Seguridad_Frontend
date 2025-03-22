# Manual de Seguridad para Frontend

🔒Este manual contiene reglas y buenas prácticas de seguridad para el equipo de frontend, basadas en OWASP. Sigue estas recomendaciones para evitar vulnerabilidades🔒


1. **Prevención de Cross-Site Scripting (XSS):**
   - **Sanea y escapa datos dinámicos:** Antes de insertar cualquier dato (como entradas de usuario o respuestas del servidor) en el DOM, esa información tiene que escaparse  manualmente. Por ejemplo:
     - Usar `textContent` en lugar de `innerHTML` para evitar la ejecución de scripts.
     - Si necesitan `innerHTML`, filtrar el contenido con una función personalizada para eliminar etiquetas o atributos peligrosos (como `<script>` o `onerror`).
   - Evita funciones como `eval()`, `setTimeout()` con strings, o `Function()` que puedan ejecutar código arbitrario.

2. **Gestión segura de datos sensibles:**
   - **Nunca** guarden información sensible (como tokens o contraseñas) en `localStorage` o `sessionStorage`, ya que son accesibles mediante XSS. Si es inevitable, conversarlo con el equipo de Backend para cifrar los datos antes de almacenarlos (es mejor buscar otra manera, ya que esto no es una solución completa).
   - Si usarán cookies para autenticación, asegúrate de que el backend las configure con `HttpOnly`, `Secure` y `SameSite`, y verifica que el frontend no las manipule directamente.
   - **Jamás** guardar información sensible en el **código** (El encargado de su repositorio debe revisar que eso nunca ocurra 👀).

3. **Validación de entradas en el cliente:**
   - Implementar validaciones en JavaScript para campos de formularios (por ejemplo, expresiones regulares para correos electrónicos o longitudes máxima de texto permitido).
   - Ejemplo:
     ```javascript
     function validarCorreo(correo) {
         const regex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
         return regex.test(correo);
     }
     ```
   - La validación definitiva la hará el equipo de backend *(según entendí, esto también mejora la experiencia del usuario y reduce intentos maliciosos iniciales).*

4. **Uso seguro de recursos externos:**
   - **Únicamnte** cargar scripts, CSS o imágenes desde fuentes confiables *(si su pc del trabajo la descargó, aún estoy por considerar si es confiable por el bitdefender)* y usar HTTPS. Ejemplo en HTML:
     ```html
     <script src="https://fuente-confiable.com/script.js"></script>
     ```
   - Evitar contenido mixto (mezclar HTTP y HTTPS), ya que los navegadores modernos lo bloquean o advierten al usuario *(lo que entiendo malogra el UX).*

5. **Protección contra Clickjacking:**
   - Aunque el encabezado `X-Frame-Options` se configura en el backend, para añadir una defensa adicional en JavaScript para detectar si la página está en un iframe:
     ```javascript
     if (window.top !== window.self) {
         document.body.style.display = "none"; // O redirigir
     }
     ```

6. **Minimización de exposición de información:**
   - No incluyan comentarios en el código (HTML o JS) que revelen detalles internos (como rutas o versiones), ya que eso facilita el entendimiento de nuestra arquitectura y los atacantes buscarán exploits. Ejemplo a evitar:
     ```html
     <!-- API endpoint: /api/v1/users -->
     ```
   - **Siempre** personalizar los mensajes de error para no mostrar stack traces o datos técnicos.

7. **Seguridad en el manejo de eventos:**
   - Al usar eventos como `onclick` o `onchange`, evita ejecutar código dinámico directamente desde atributos HTML. En lugar de:
     ```html
     <button onclick="eval(userInput)">Clic</button>
     ```
     Usar:
     ```javascript
     document.querySelector("button").addEventListener("click", function() {
         // Lógica segura aquí
     });
     ```

8. **Integridad de scripts:**
   - Si cargas scripts externos (como librerías CDN), usar el atributo `integrity` con un hash SRI (Subresource Integrity) para garantizar que no se alteren:
     ```html
     <script src="https://cdn.ejemplo.com/libreria.js" 
             integrity="sha384-oqVuAfXRKap7fdgcCY5uykM6+R9GqQ8K/uxy9rx7HNQlGYl1kPzQho1wx4JwY8wC" 
             crossorigin="anonymous"></script>
     ```

9. **Manejo seguro de dependencias:**
   - Si usan librerías JavaScript externas, descárguenlas y sírvanlas localmente en lugar de depender de CDNs, o verifiquen regularmente su seguridad. Revisar vulnerabilidades conocidas en sitios como CVE o Snyk.

10. **Pruebas de seguridad:**
    - Usar herramientas manuales o extensiones de navegador (como Chrome DevTools) para inspeccionar el DOM y detectar posibles puntos de inyección.
    - Prueba la aplicación con entradas maliciosas (por ejemplo, `<script>alert('xss')</script>`) para asegurarte de que no se ejecuten.
    - Al final el equipo de Back los va a atacar para ver si siguieron estas reglas 😄.

### Consideraciones prácticas:
Dado que no usarán frameworks, la responsabilidad de implementar estas medidas recae totalmente en ustedes, porque un framework ya tiene ciertas consideraciones de seguridad, lo que requiere mayor disciplina en el código. Por ejemplo:
- Crea funciones reutilizables en JavaScript para escapar HTML:
  ```javascript
  function escaparHTML(texto) {
      const mapa = {
          '&': '&amp;',
          '<': '&lt;',
          '>': '&gt;',
          '"': '&quot;',
          "'": '&#x27;'
      };
      return texto.replace(/[&<>"']/g, match => mapa[match]);
  }
  ```
- Mantengan el CSS limpio de inyecciones (eviten `url()` dinámicos no validados).
