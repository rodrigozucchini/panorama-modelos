# 2. Panorama de modelos

> **Snapshot: noviembre de 2026.** Los nombres y los rankings caducan en semanas; los principios no. Este documento lo escribió Claude (Anthropic), que es uno de los modelos listados: contrastá los rankings con fuentes independientes y, sobre todo, con tus propias pruebas (sección 5).

## Índice

1. [First principles](#1-first-principles)
2. [Modelos frontier](#2-modelos-frontier)
3. [Familias open-weight](#3-familias-open-weight)
4. [Cómo elegir](#4-cómo-elegir)
5. [Benchmarks y rankings, y sus límites](#5-benchmarks-y-rankings-y-sus-límites)
6. [Testear modelos con tus propias tareas](#6-testear-modelos-con-tus-propias-tareas)

---

## 1. First principles

Un LLM es, en esencia, **un archivo gigante de números (los pesos) más un programa que los usa para predecir el siguiente token**. Todo lo demás (chat, agentes, "razonamiento") se construye encima de esa predicción.

Tres restricciones explican por qué existe un "panorama":

1. **Entrenar es carísimo** → solo unos pocos laboratorios llegan a la frontera.
2. **Usar el modelo también cuesta**: cada token generado ocupa GPUs, y un modelo más grande es más caro y más lento. De ahí los **tiers** (grande / mediano / chico).
3. **Los pesos se pueden publicar o no**: si el laboratorio solo vende acceso por API, es **closed-weight**; si los publica, es **open-weight**.

Esas tres restricciones se transforman en las seis cosas que importan al elegir: **calidad, costo, latencia, contexto, licencia y privacidad** (sección 4).

Dos ideas más:

- **Un modelo ya no es un número fijo.** El comportamiento depende del pre-entrenamiento, del post-entrenamiento y de cuánto "piensa" al responder (niveles de esfuerzo tipo `low / medium / high`). Pensá en un modelo como un punto en un espacio: modelo + esfuerzo + prompt + herramientas.
- **MoE (Mixture of Experts):** el modelo tiene muchos "expertos" pero activa pocos por token. Rinde como uno grande con costo de inferencia de uno más chico, pero necesitás memoria para *todos* los pesos.

---

## 2. Modelos frontier

**Frontier** = los modelos más capaces del momento, casi siempre closed-weight (solo por API o producto).

Casi todos los laboratorios ofrecen la misma escalera:

```
[ Top ]     → tareas difíciles (agentes largos, investigación, código complejo)   más capaz, caro, lento
[ Medio ]   → el caballito de batalla para producción
[ Chico ]   → alto volumen, baja latencia, clasificación, extracción              menos capaz, barato, rápido
```

| Laboratorio | Familia | Modelos recientes | Rasgos |
|---|---|---|---|
| **Anthropic** | **Claude** | Fable 5.2 (tope), Opus 5.5, Sonnet 5.5, Haiku 5.5 | Fuerte en código, agentes de larga duración, escritura y seguimiento de instrucciones; contexto de hasta 1M tokens |
| **OpenAI** | **GPT** | GPT-6.2 Astra (frontier) y GPT-6.2 Sol (eficiente), lanzados el 17/11/2026; GPT-6 Luna (chico) | Ecosistema más grande (ChatGPT, API, Codex); fuerte en razonamiento y herramientas |
| **Google** | **Gemini** | Gemini 4: Carbon (el más capaz, 10/11/2026), Hydrogen, Neon, Argon; línea Flash | Multimodal nativo (texto, imagen, audio, video, PDF), ~1M tokens de contexto, integración con Google Cloud |
| **xAI** | **Grok** | Línea Grok 4.x | Integración con X y datos en tiempo real |

Qué los diferencia de verdad, más allá del ranking:

- **Comportamiento en agentes:** cuánto aguantan una tarea de muchos pasos sin perderse.
- **Seguimiento de instrucciones y formato:** ¿devuelve JSON válido siempre?
- **Estilo:** tono, verbosidad, cuándo se niega, cuándo dice "no sé".
- **Ecosistema:** SDKs, caché de prompts, batch, y si está en la nube que ya usás (Bedrock, Vertex, Azure).
- **Riesgo de proveedor:** en junio de 2026 Anthropic suspendió el acceso a Fable 5 y Mythos 5 por controles de exportación de EE. UU. y lo restauró el 1 de julio. Depender de un solo modelo de un solo proveedor te deja expuesto a eventos así.

> Los laboratorios renombran y sacan variantes cada pocas semanas: verificá siempre el **ID exacto** del modelo en la documentación oficial.

---

## 3. Familias open-weight

### 3.1 Open-weight ≠ open-source

| Concepto | Te dan | No te dan |
|---|---|---|
| **Closed-weight** | Acceso por API | Los pesos |
| **Open-weight** | Los pesos para descargar y correr | Normalmente ni los datos ni el código de entrenamiento; la licencia puede tener restricciones |
| **Open-source estricto** | Pesos + código + datos + licencia permisiva | (raro en modelos grandes) |

Casi todo lo que llaman "open source" en LLMs es en realidad open-weight. Las licencias van de muy permisivas (Apache 2.0, MIT) a con condiciones (límites de uso comercial, atribución): **leé siempre la licencia**.

**Por qué un laboratorio publica los pesos:** para ganar adopción y ecosistema, para reputación y reclutamiento, o porque le conviene que el modelo sea gratis y vender infraestructura o servicios alrededor.

### 3.2 Las familias

| Familia | Origen | Qué conviene saber |
|---|---|---|
| **DeepSeek** (V4 Pro, V4.1 Flash) | China | Pionero en eficiencia y razonamiento abierto. La línea Pro es un MoE de ~1.6T parámetros; la Flash apunta a velocidad y costo |
| **Qwen** (3.8, 4-Max) | Alibaba, China | Familia enorme, de menos de 1B a gigantes MoE. Las versiones chicas (ej. 27B) rinden muy bien para su tamaño. Las variantes "Max" suelen ser solo por API |
| **Llama** (Llama 4) | Meta, EE. UU. | Fue el estándar abierto de 2023-2025; hoy perdió terreno frente a los modelos chinos, pero sigue teniendo muchísimas herramientas y fine-tunes |
| **Mistral** | Francia | Referente europeo; en octubre de 2026 liberó los pesos de un modelo de ~1T parámetros. Interesante si importa la soberanía de datos en la UE |
| **Gemma** (Gemma 4) | Google | Modelos chicos para correr en laptops, celulares y GPUs modestas |
| **Kimi** (K2.6, K3) | Moonshot AI, China | K3: MoE de ~2.8T parámetros, contexto de 1M tokens, multimodal. Entre los abiertos más fuertes, pero tan grande que casi nadie lo corre por su cuenta |
| **GLM** (GLM-5.x) | Z.ai, China | Muy fuerte en código y agentes, a una fracción del costo de los cerrados |
| **MiniMax** (M-series) | China | Orientado a agentes y contexto largo, buen balance costo/calidad |

### 3.3 ¿Qué tan cerca están de los cerrados?

La brecha en el índice de Artificial Analysis se achicó de ~13 puntos (2025) a unos 3-6 durante 2026. Pero cuando lanzaron la versión 5 del índice (27/10/2026), con mayoría de datasets privados, la brecha volvió a ~12 puntos y varios modelos abiertos populares perdieron más de 10 puntos sin haber cambiado.

**Lección:** el tamaño de la brecha depende de *cómo se mide*. Parte de la ventaja aparente de los abiertos venía de que los benchmarks públicos se pueden "estudiar" (ver sección 5).

### 3.4 Tres formas de usarlos

| Camino | Ventaja | Desventaja |
|---|---|---|
| **API de un proveedor de inferencia** (Together, Fireworks, OpenRouter…) | Cero infraestructura, precios bajos | Tus datos pasan por un tercero; la calidad puede variar entre proveedores (cuantización) |
| **Self-hosting** (GPUs alquiladas, vLLM) | Control total de datos | Necesitás ingeniería, y las GPUs cuestan aunque no las uses |
| **Local** (Ollama, llama.cpp) | Privacidad máxima, offline | Solo modelos chicos o medianos |

---

## 4. Cómo elegir

No existe "el mejor modelo", sino el mejor para tu tarea, tu presupuesto y tus restricciones. Cada dimensión es una perilla con costo: mejorar una suele empeorar otra. Regla práctica: **empezá por un tier chico o medio y subí solo si tus tests lo justifican.**

### Calidad
No es un número único: un modelo puede ser excelente en código y flojo redactando en español rioplatense. Se mide en **tu** tarea (sección 6), no en el ranking general.

### Costo
```
costo por llamada = tokens_entrada × precio_in + tokens_salida × precio_out
```
- **Los tokens de razonamiento se cobran como salida.** Un modelo "barato por token" que piensa mucho puede salir más caro que uno "caro" que responde directo. Mirá el **costo por tarea resuelta**, no el precio de lista.
- La salida cuesta varias veces más que la entrada.
- **Caché de prompts** y **batch** (asíncrono, con descuento) reducen mucho el costo cuando repetís prefijos largos o no necesitás respuesta inmediata.
- Orden de magnitud por millón de tokens de salida (nov 2026): modelos top ~USD 40, tier eficiente ~USD 7-10, chicos o abiertos vía API ~USD 1-5. Son referencias, los precios cambian.

### Latencia
- **TTFT** (tiempo hasta el primer token) importa en chat; **tokens por segundo** importa en respuestas largas.
- **Razonar es esperar:** los tokens de pensamiento se generan antes de la respuesta visible.
- En agentes con muchas llamadas encadenadas la latencia se multiplica: un modelo chico para los pasos simples y uno grande solo para los difíciles suele ganar.

### Contexto
La ventana es la **memoria de trabajo** de una consulta (entrada + razonamiento + salida), no memoria permanente. Los modelos top llegan a ~1M tokens, pero:
- **Ventana anunciada ≠ ventana útil:** muchos modelos pierden precisión cuando el dato relevante está enterrado en el medio. Probalo con tus documentos.
- Más contexto = más costo y más latencia.
- **Contexto largo vs. RAG:** contexto largo es más simple para pocos documentos grandes; RAG (recuperar solo lo relevante) es más barato y escala a bases enormes.

### Licencia
Si es open-weight: ¿puedo usarlo comercialmente, hacer fine-tuning, redistribuir? Si es cerrado: ¿qué dicen los términos de la API sobre usos prohibidos y sobre usar las salidas para entrenar otros modelos?

### Privacidad
Cada vez que mandás datos a una API, salen de tu perímetro. Preguntas clave: ¿el proveedor guarda mis prompts y por cuánto tiempo?, ¿los usa para entrenar?, ¿dónde se procesan (residencia de datos)?, ¿hay acuerdos y certificaciones (DPA, SOC 2)?

```
API pública → API empresarial → Nube privada (Bedrock/Vertex/Azure) → Self-hosted open-weight → Local
(menos control)                                                                               (más control)
```
Más control también significa más responsabilidad: seguridad, parches y disponibilidad pasan a ser tuyos.

### Diseñá para poder cambiar de modelo
Todo este panorama va a envejecer en meses. Poné una interfaz `LLMProvider` con implementaciones intercambiables (en NestJS, un provider inyectable), fijá el **ID exacto con versión** en producción (los alias tipo `latest` pueden cambiar el comportamiento sin aviso) y tené un fallback por si un proveedor cae.

---

## 5. Benchmarks y rankings, y sus límites

Un benchmark es un **experimento controlado**: mismas preguntas para todos los modelos, misma forma de puntuar. Sirve para comparar sin leer miles de respuestas a mano, pero es un **proxy**: mide algo *parecido* a lo que te importa, no exactamente lo que te importa.

### LMArena (hoy "Arena")
- **Mide:** preferencia humana. Una persona manda un prompt, recibe dos respuestas anónimas y vota la que prefiere. Los votos se convierten en un puntaje tipo Elo (Bradley-Terry).
- **Límites:**
  - Mide **popularidad, no corrección**: los humanos tienden a preferir respuestas más largas, amables o mejor formateadas aunque sean menos exactas.
  - Los prompts los pone el público, con mucho peso de consultas casuales que no reflejan, por ejemplo, extraer datos de facturas.
  - Diferencias chicas entre posiciones suelen estar dentro del margen de error: mirá el intervalo de confianza y la cantidad de votos.

### Artificial Analysis (Intelligence Index)
- **Mide:** un compuesto de varias evaluaciones (razonamiento, código, agentes, conocimiento) en escala 0-100. Además publica **costo por tarea y velocidad**, y eso lo hace muy útil para decidir.
- **Límites:**
  - Un promedio esconde diferencias: dos modelos con el mismo puntaje pueden ser muy distintos en código vs. escritura.
  - El índice **cambia de versión** y los ranking se mueven (el caso de la v5 en la sección 3.3): los números son relativos a la versión.
  - Suele reportar el máximo esfuerzo de razonamiento, que es el techo y no necesariamente cómo lo vas a usar.

### SWE-bench (y su familia)
- **Mide:** si un modelo, dentro de un agente con herramientas, resuelve **issues reales de GitHub**: recibe el repo y el problema, genera un parche y se corren los tests del proyecto.
- **Límites:**
  - Mide el **sistema, no solo el modelo**: el resultado depende del agente, las herramientas y los reintentos.
  - **Saturación y contaminación:** las versiones más conocidas están cerca del techo y los repos pudieron estar en los datos de entrenamiento. Por eso aparecen variantes más duras (SWE-bench Pro) y otros benchmarks de agentes (Terminal-Bench).
  - Cubre sobre todo Python en repos open-source; tu código privado en TypeScript puede comportarse distinto.

### Límites que valen para todos

| Problema | Explicación |
|---|---|
| **Ley de Goodhart** | Cuando una medida se vuelve objetivo, deja de ser buena medida: si el ranking vende, los laboratorios optimizan para el ranking |
| **Contaminación** | Si las preguntas están en internet, pudieron colarse en el entrenamiento y el modelo "recuerda" en vez de razonar |
| **Saturación** | Cuando los top sacan 95%+, el benchmark ya no distingue |
| **Cifras del fabricante** | Son afirmaciones, no mediciones independientes, y suelen usar la configuración más favorable |
| **Configuraciones no comparables** | Nivel de esfuerzo, intentos (pass@1 vs pass@k), herramientas y prompts cambian el resultado |
| **Proxy ≠ tarea** | Tu tarea tiene tus datos, tu idioma y tus casos borde |
| **Falta costo y latencia** | Podrías pagar 8× más por 2 puntos de calidad |

### Cómo leer un ranking sin que te engañe
1. Mirá la **fecha y la versión** del índice.
2. Mirá la **incertidumbre**: si dos modelos están a 5 puntos con ±8 de margen, son empate.
3. Cruzá **al menos dos fuentes independientes** con metodologías distintas (preferencia humana + tareas verificables).
4. Buscá el **costo por tarea**, no solo la calidad.
5. Usá el ranking para armar una **lista corta de 3-5 candidatos**, no para decidir.

> **Un benchmark te dice con quién probar, no a quién elegir.**

---

## 6. Testear modelos con tus propias tareas

Tu tarea tiene una distribución específica (tus documentos, tu idioma, tu formato), y además tus datos privados son lo único que los modelos no vieron en el entrenamiento. Una evaluación propia es la única evidencia directa.

```
eval = dataset (casos de prueba) + grader (criterio de éxito) + harness (corre todo y registra)
```

### Paso a paso

1. **Definí qué es "bien hecho"** con palabras concretas. Ej.: "extraer CUIT, fecha y total de una factura en JSON; correcto = los tres campos coinciden con la etiqueta humana".
2. **Armá un dataset chico pero representativo.** Con 30-100 casos reales (anonimizados) alcanza para empezar. Incluí los típicos y, sobre todo, los **difíciles y los casos borde**. Guardá una parte "ciega" que no uses para ajustar el prompt.
3. **Elegí cómo puntuar**, de más a menos confiable:

   | Grader | Ejemplo | Cuidado |
   |---|---|---|
   | **Programático** | JSON válido, campo == esperado, los tests pasan | Solo sirve si hay respuesta verificable |
   | **Reglas / similitud** | Contiene X, no contiene Y | Rígido |
   | **LLM-as-judge** | Otro modelo puntúa con una rúbrica | Tiene sesgos (prefiere respuestas largas y de su propia familia): validalo contra una muestra humana |
   | **Humano** | Alguien revisa | Lento y caro: usalo para calibrar |

4. **Corré todos los candidatos con el mismo prompt y configuración**, y después optimizá el prompt para cada uno.
5. **Repetí 3-5 veces cada caso**: los modelos son estocásticos y una sola corrida puede ser suerte. Un 90% inestable puede ser peor que un 85% estable.
6. **Medí más que calidad:** tokens (incluidos los de razonamiento), latencia (TTFT y total, en percentiles p50/p95), costo y tasa de errores o formato inválido.
7. **Calculá el costo por tarea correcta** (`costo total / casos resueltos`) y elegí **el más barato que supere tu umbral de calidad**.
8. **Dejala como prueba de regresión** en el repo: correla al cambiar de modelo o de prompt, y cuando falle algo en producción, agregá ese caso al dataset.

### Ejemplo mínimo (TypeScript)

Agnóstico del proveedor: vos implementás `callModel` con el SDK que uses.

```ts
type Caso = { id: string; entrada: string; esperado: Record<string, string> };

// Implementá con el SDK del proveedor: debe devolver texto y tokens usados
async function callModel(modelo: string, prompt: string) {
  // return { texto, tokensIn, tokensOut };
  throw new Error('implementar callModel');
}

// Precios en USD por millón de tokens (sacalos de la doc oficial)
const PRECIOS: Record<string, { in: number; out: number }> = {
  'modelo-chico': { in: 0.2, out: 1 },
  'modelo-top':   { in: 5,   out: 25 },
};

// Grader programático: ¿el JSON coincide con lo esperado?
function ok(texto: string, esperado: Record<string, string>) {
  try {
    const obj = JSON.parse(texto);
    return Object.entries(esperado).every(([k, v]) => String(obj[k]).trim() === v);
  } catch {
    return false; // JSON inválido = error
  }
}

async function evaluar(modelo: string, casos: Caso[], repeticiones = 3) {
  const p = PRECIOS[modelo];
  let aciertos = 0, total = 0, costo = 0;
  const latencias: number[] = [];

  for (const caso of casos) {
    for (let i = 0; i < repeticiones; i++) {
      const t0 = performance.now();
      const r = await callModel(modelo, `Devolvé SOLO un JSON.\n\n${caso.entrada}`);
      latencias.push(performance.now() - t0);
      costo += (r.tokensIn * p.in + r.tokensOut * p.out) / 1e6;
      total++;
      if (ok(r.texto, caso.esperado)) aciertos++;
    }
  }

  latencias.sort((a, b) => a - b);
  return {
    modelo,
    precision: aciertos / total,
    p50Ms: Math.round(latencias[Math.floor(total * 0.5)]),
    p95Ms: Math.round(latencias[Math.floor(total * 0.95)]),
    costoPorAcierto: aciertos ? costo / aciertos : Infinity,
  };
}
```

### Trampas comunes
- **Evaluar con 5 ejemplos:** eso es anécdota, no estadística.
- **Ajustar el prompt mirando el set de prueba:** sobreajustás; guardá casos ciegos.
- **Comparar con prompts o esfuerzos distintos** sin avisar.
- **Confiar ciegamente en LLM-as-judge:** usá un juez de otra familia que los modelos evaluados.
- **Mirar solo cuánto acierta:** mirá también *cómo* falla (¿alucina?, ¿rompe el formato?, ¿se niega?).

---

## Resumen

1. Un modelo es un punto en el espacio **calidad × costo × latencia × contexto × licencia × privacidad**; no hay un "mejor" absoluto.
2. Los **frontier** (cerrados) lideran en capacidad; los **open-weight** dan control, privacidad y costos bajos, con una brecha que cambia según cómo se mide.
3. Los **benchmarks** sirven para armar la lista corta, no para decidir.
4. La única evidencia directa es una **eval propia** con tus datos, midiendo calidad, costo y latencia.
5. Diseñá para **poder cambiar de modelo**: todo esto va a envejecer en meses.
