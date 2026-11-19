# Vigor Prime — paquete independiente

## Archivos para publicar en GitHub Pages
Sube el contenido de esta carpeta manteniendo esta estructura. Para una web nueva, coloca `index.html`, `manifest.json`, `sw.js`, `icon.svg`, `supabase-client.js` y `config.example.js` en la raíz del repositorio. La carpeta `supabase/` es para configurar servicios en la nube y no es necesaria para que funcione el modo local.

## Qué incluye
- App basada en el último `index(2).html` aportado: entrenamientos, generador de rutinas, agenda, GPS/carreras, nutrición, sueño, progreso, coach local y temporizador.
- Base integrada ampliada: catálogo de ejercicios y alimentos con valores nutricionales orientativos. Los valores pueden variar según marca, cocción y porción; consulta etiquetas para precisión.
- Estética Vigor Prime: grafito, negro y acento titanio/champagne discreto.
- PWA instalable: manifest, icono SVG y service worker con caché offline.
- Copia de seguridad y restauración manual de datos en JSON desde la barra superior.
- Preparación opcional de Supabase para cuenta, sincronización y función de coach IA.

## Publicar en GitHub Pages
1. Crea un repositorio nuevo (no uses el original).
2. Sube todos los archivos de esta carpeta conservando `supabase/functions/fitness-coach/index.ts` y `supabase/schema.sql`.
3. En GitHub: Settings → Pages → Deploy from a branch → `main` → `/ (root)`.
4. Espera a que Pages publique la web. Abre la URL en Chrome Android y usa menú ⋮ → Instalar aplicación (o Añadir a pantalla de inicio).

## Datos locales
La app funciona sin cuenta ni claves. Los datos se guardan en el almacenamiento del navegador del dispositivo. Usa **Exportar datos** regularmente y guarda el JSON en un lugar seguro. **Importar copia** reemplaza los datos locales actuales.

## Activar nube (opcional; requiere proyecto Supabase)
1. Crea un proyecto en Supabase.
2. Ejecuta `supabase/schema.sql` en SQL Editor.
3. Copia `config.example.js` como `config.js` y añade Project URL y la clave `anon`/publishable del proyecto. No uses `service_role`.
4. Publica `config.js` en la raíz si aceptas que esos datos públicos sean visibles; la clave anon es pública y la seguridad depende de RLS.
5. `supabase-client.js` contiene helpers de registro, inicio de sesión y sincronización. La interfaz de login/sync aún debe conectarse desde la pantalla de ajustes antes de que el usuario pueda usarlos.

## Activar coach IA real (opcional; puede tener coste)
1. Despliega la Edge Function `fitness-coach` en Supabase.
2. Configura `OPENAI_API_KEY` como secreto de la función (`supabase secrets set OPENAI_API_KEY=...`). Nunca la escribas en `config.js` ni en el navegador.
3. La función requiere autenticación de usuario. La llamada se hace desde `VigorCloud.askCoach()` tras iniciar sesión.
4. El uso del proveedor de IA puede generar costes según el modelo y el consumo; no se promete uso gratuito.

## Importante
Esta entrega sí está preparada para funcionar offline y guardar datos localmente. La nube y la IA externa son opcionales y **no quedan activadas solo por subir los archivos**: necesitan crear/configurar Supabase, desplegar la función y conectar la pantalla de acceso. El coach local existente sigue funcionando sin esos servicios.


## Escáner nutricional (actualización)

- Escaneo de EAN-8/EAN-13/UPC con cámara cuando el navegador soporta `BarcodeDetector` (HTTPS y permiso de cámara).
- Búsqueda manual por código de barras y búsqueda de productos online.
- Consulta de fichas de Open Food Facts para obtener energía, proteínas, hidratos, grasas, saturadas, fibra, sal, ingredientes y alérgenos cuando esos campos están disponibles.
- Recomendaciones adaptadas al objetivo configurado (déficit/definición, volumen o mantenimiento), sin presentar alimentos como prohibidos.
- Comparación orientativa con alternativas de la base local.

**Cobertura Mercadona:** no se incluye ni se afirma tener un volcado completo del catálogo de Mercadona. El escáner consulta Open Food Facts por EAN o búsqueda y solo puede mostrar los productos que tengan una ficha con datos suficientes. Los productos Hacendado/Mercadona que no estén en esa base no aparecerán hasta que se añada su ficha o se consulte una fuente de catálogo autorizada. Los datos comunitarios pueden estar incompletos o desactualizados: comprueba siempre la etiqueta del envase.

Open Food Facts es una base colaborativa. La integración consulta su API pública y no copia un catálogo completo a la app. Consulta sus condiciones y atribución en https://world.openfoodfacts.org/ y https://openfoodfacts.github.io/documentation/.

### Escala de adecuación A–E en el escáner
El escáner muestra una calificación A, B, C, D o E ajustada al objetivo elegido en el perfil (déficit/definición, volumen o mantenimiento), junto con una puntuación orientativa y los factores que influyen. La valoración usa los datos disponibles por 100 g (energía, proteína, grasa, saturadas, fibra, sal y azúcares cuando existen). No es una clasificación médica ni declara alimentos prohibidos: la ración, la frecuencia, el contexto de la dieta y la calidad de los datos importan. Si la ficha externa no incluye un nutriente, ese dato no puede valorarse correctamente.
