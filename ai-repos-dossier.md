# Dossier de repositorios, skills y herramientas de IA

Fecha de la investigación: 2026-11-14. Idioma: español.

Este documento ordena todo lo que me pasaste en capturas y mensajes: repositorios de GitHub, skills, páginas y servicios. Para cada repositorio hay un enlace, quién lo publica, su licencia, para qué sirve, cómo se instala, una nota de seguridad y la fuente de la información.

---

## 0. Cómo leer este documento (método y límites)

**Cómo investigué.** Hice dos cosas distintas:
1. **Búsquedas web** para entender qué es cada cosa y descubrir enlaces.
2. **Lectura directa de la página de GitHub** de cada repositorio, para confirmar quién es el dueño real, la licencia, el tamaño, el método de instalación y las advertencias del propio README. Lo que aparece como "✔ verificado" lo leí en GitHub el 2026-11-14.

**Límites que debes conocer:**
- **No audité código.** Mis notas de seguridad salen del README y de la estructura visible del repo (carpetas `hooks/`, `scripts/`, `package.json`, instaladores). No abrí los scripts. Una nota "B" significa "no vi señales de alarma en el README", no "revisé el código y es seguro".
- **No pude consultar Reddit.** Mi buscador devuelve error para `reddit.com`, así que no hay hilos de Reddit en este informe. Para orquestadores usé las fuentes que sí pude leer: GitHub, listas "awesome" y blogs técnicos. Cada una está marcada como fuente secundaria.
- **Las estrellas de GitHub no prueban calidad ni seguridad.** Algunos números son anómalos (por ejemplo, REA muestra 457.7k estrellas y 112k forks). Las estrellas se pueden inflar. Trátalas como una pista débil.
- **Cifras como snapshot.** Las estrellas y versiones cambian cada día. Todas son de la fecha indicada arriba.
- **Lo que no encontré** está listado en la sección 13.

---

## 1. Índice

0. Cómo leer este documento (método y límites)
2. Conceptos explicados (incluye "extraer, no instalar")
3. Publicadores oficiales: qué cuenta y qué no
4. Seguridad: escala de notas
5. Agente orquestador: ¿propio o existente?
6. Playwright headless en 6 carriles y computer use
7. Catálogo A: repositorios oficiales
8. Catálogo B: los 18 repositorios de tu primera consulta (alfabético)
9. Catálogo C: los repositorios de tu segunda consulta
10. Catálogo D: orquestadores y herramientas de carriles
11. Catálogo E: páginas, servicios y funciones (sin repositorio)
12. Tabla maestra de seguridad
13. Lo que no pude verificar o resolver
14. Conceptos de IA de básico a avanzado (libro de Snowflake)
15. Conectores y MCP en Claude Code
16. Cursos gratuitos de Anthropic y certificados
17. Seguridad al ir rápido (API keys, repos, agentes)
18. Ruta de aprendizaje sugerida
19. Proyectos de IA valorados para el portafolio (post de Instagram)
20. El iceberg de Claude Code: 30 términos
21. Fuentes

---

## 2. Conceptos explicados

**Repositorio (repo).** Una carpeta de código o archivos alojada en GitHub, con historial de cambios. "Clonar" es copiarla a tu computador.

**Skill (habilidad de agente).** Una carpeta con un archivo `SKILL.md` que contiene instrucciones en texto para que un agente (Claude Code, Codex, etc.) haga una tarea de cierta manera. Puede incluir también scripts y recursos. Lo importante: **el texto de una skill lo lee y obedece el modelo**. Por eso una skill maliciosa no necesita código; le basta una frase convincente.

**Plugin.** Un paquete que puede juntar skills, comandos, hooks y servidores MCP. En Claude Code se instala con `/plugin marketplace add ...` y `/plugin install ...`.

**Hook.** Un script que el agente ejecuta automáticamente en ciertos momentos (al iniciar sesión, antes de usar una herramienta, al editar un archivo). Es lo más delicado de revisar: **se ejecuta con tus permisos sin que lo pidas cada vez**.

**MCP (Model Context Protocol).** Un estándar para conectar un agente con herramientas externas (navegador, base de datos, Miro, etc.). Un servidor MCP es un programa que el agente puede llamar.

**Agente orquestador.** Un programa que reparte trabajo entre varios agentes (o varias sesiones), controla cuál hace qué, junta los resultados y aplica reglas (presupuesto, aprobaciones, pruebas). Hay tres tipos distintos (sección 5).

**Git worktree.** Una forma de tener varias copias de trabajo del mismo repo, cada una en su propia rama, para que varios agentes editen en paralelo sin pisarse. Es la técnica de aislamiento más usada por los orquestadores de código.

**Headless.** Un navegador que corre sin ventana visible. Sirve para automatizar sin pantalla y es más liviano.

**Carril (lane).** Tu término para una sesión de navegador independiente que trabaja en paralelo. "6 carriles" = 6 navegadores aislados trabajando a la vez (sección 6).

**Computer use.** Una capacidad de Claude para controlar un escritorio completo (ver pantalla, mover el mouse, escribir). Es distinta de Playwright: Playwright controla un navegador con comandos precisos, computer use maneja "como una persona" cualquier programa. Es más flexible y más riesgoso.

**Prompt injection (inyección de instrucciones).** Cuando contenido externo (una página web, un PDF, una oferta de trabajo, un README) trae texto que intenta darle órdenes al agente. Anthropic lo advierte expresamente para computer use: Claude puede seguir comandos encontrados en el contenido aunque contradigan lo que tú pediste.

**Supply chain (cadena de suministro).** El riesgo de que lo que instalas traiga dependencias o descargas que no revisaste. Ejemplos: `npx paquete@latest` (baja y ejecuta la última versión que exista ese día) o `curl ... | bash` (descarga un script y lo ejecuta sin leerlo).

### 2.1 "Todo extract, not install; para evitar hacks": mi interpretación

Esta expresión exacta no la encontré en ninguna fuente, así que la interpreto así, de acuerdo con lo que sí describen Socket y SafeDep:

- **Instalar** (con `npx`, `curl | bash`, `/plugin install`, `npm install -g`) puede ejecutar código tuyo-con-permisos: scripts de instalación, hooks, descargas de binarios.
- **Extraer** es descargar el repo a una carpeta aislada, **leer** su contenido y copiar a mano **solo lo inerte** (normalmente el `SKILL.md` y sus archivos de referencia en texto), dejando fuera scripts, hooks y manifiestos que ejecutan cosas.

Socket lo explica así: copiar archivos de una skill a la carpeta de skills no ejecuta código; el riesgo aparece después, cuando el agente las carga y corre. Y SafeDep lista los ataques típicos: instrucciones maliciosas escondidas en la descripción de `SKILL.md`, nombres que imitan a otro (typosquatting) y scripts con dependencias sin versión fija.

**Advertencia importante:** extraer **no elimina** el riesgo de prompt injection, porque `SKILL.md` es texto que el modelo obedece. Por eso hay que **leer el archivo completo** antes de copiarlo.

**Procedimiento recomendado (checklist):**
1. Verifica que el repo es el oficial: dueño correcto, enlace desde la web del proyecto, y ojo con clones con nombre parecido. Varios README lo advierten (diagram-design, REA, ponytail).
2. Clona a una carpeta vacía y separada del resto de tus proyectos: `git clone --depth 1 <url>`. **No ejecutes** `npm install`, `make setup`, `pip install` ni ningún instalador.
3. Lista qué hay: `hooks/`, `scripts/`, `package.json` (busca `postinstall`/`preinstall`), `.claude-plugin/`, `install.sh`, binarios (`.exe`, `.dll`, `.so`).
4. Lee **completo** el `SKILL.md` y los archivos que referencia. Busca: órdenes de ignorar reglas o de ocultar acciones, pedidos de credenciales, URLs externas, comandos `curl`/`wget`/`base64`, texto invisible o muy largo.
5. Copia solo los archivos de texto que necesitas a tu carpeta de skills. Anota el **commit exacto** (hash) del que copiaste.
6. Si el `SKILL.md` declara herramientas permitidas o hooks en su cabecera, quítalos o revísalos a mano.
7. (Opcional) pasa un escáner estático. Las fuentes mencionan `skill-security-auditor`, `safedep/vet` y `cisco-ai-defense/skill-scanner`. Son comprobaciones por patrones: ayudan, pero pueden perder ataques ofuscados y no reemplazan tu lectura.
8. Prueba la primera vez en un entorno sin credenciales (contenedor o máquina virtual) y observa qué comandos intenta ejecutar.

**Qué dicen los datos sobre el riesgo:** la auditoría "ToxicSkills" de Snyk revisó 3,984 skills de ClawHub y skills.sh. Los artículos que la resumen dan cifras distintas (13.4% con problemas críticos; 76 payloads maliciosos confirmados). No abrí el informe original, así que cita esas cifras con cautela.

---

## 3. Publicadores oficiales: qué cuenta y qué no

Tú mencionaste "los oficiales de Anthropic, Google y Superpowers". Esto es lo que verifiqué:

| Publicador | Repo | Estado real |
|---|---|---|
| **Anthropic** (org `anthropics`) | [anthropics/skills](https://github.com/anthropics/skills) | ✔ Oficial. 194.1k ⭐. Skills de ejemplo + skills de documentos (docx, pdf, pptx, xlsx). Dice ser "para demostración y educación"; hay que probarlas antes de usarlas en algo crítico. |
| **Anthropic** | [anthropics/claude-quickstarts](https://github.com/anthropics/claude-quickstarts) (contiene `computer-use-demo`) | ✔ Oficial. MIT. |
| **Anthropic** | [anthropics/claude-agent-sdk-python](https://github.com/anthropics/claude-agent-sdk-python) | ✔ Oficial. MIT. Base para construir tu propio orquestador (sección 5). |
| **Anthropic** | [anthropics/claude-code-action](https://github.com/anthropics/claude-code-action) | Oficial según la lista awesome (no lo abrí). |
| **Google** (org `google`) | [google/skills](https://github.com/google/skills) | ✔ Oficial. Apache-2.0. 24.1k ⭐. Skills de Google Cloud; se instala con `npx skills add google/skills` o por marketplace. Anunciada por Google Cloud el 22-abr-2026 según su blog. |
| **Google Cloud** | [GoogleCloudPlatform/scion](https://github.com/GoogleCloudPlatform/scion) | ✔ Org de Google, pero el README dice **"no es un producto oficialmente soportado por Google"** y es pre-1.0. |
| **Google** | [google-github-actions/run-gemini-cli](https://github.com/google-github-actions/run-gemini-cli) | Oficial según la lista awesome (no lo abrí). |
| **Microsoft** | [microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp) | ✔ Oficial. Apache-2.0. 39.6k ⭐. |
| **OpenAI** | [openai/openai-agents-python](https://github.com/openai/openai-agents-python) | ✔ Oficial. MIT. Compatible con otros modelos. |
| **AWS Labs** | [awslabs/cli-agent-orchestrator](https://github.com/awslabs/cli-agent-orchestrator) | ✔ Org `awslabs` (laboratorio de AWS, no un servicio comercial soportado). Apache-2.0. |
| **LangChain** | [langchain-ai/langgraph](https://github.com/langchain-ai/langgraph) | ✔ Empresa conocida. MIT. |
| **"Superpowers"** | [obra/superpowers](https://github.com/obra/superpowers) | ✔ **No es de Anthropic ni de Google.** Es de Jesse Vincent (usuario `obra`, con su empresa Prime Radiant). MIT. 317.2k ⭐. Su README lista el marketplace oficial de Claude Code entre las vías de instalación; eso significa que aparece en un catálogo, **no** que Anthropic sea el autor. La documentación de Claude Code aclara que la mayor parte de lo que lista el marketplace oficial viene de socios y otros autores, no de Anthropic. |

**Regla práctica:** "oficial" te dice **quién es el dueño**, no que no haya riesgos. Un repo oficial sigue pudiendo traer scripts, y todo lo que se ejecuta con tus permisos merece lectura. Pero un dueño identificable, con empresa detrás y política de seguridad, baja mucho la probabilidad de un ataque deliberado.

Dos precisiones útiles:
- Ninguno de los 18 repositorios de tu primera lista pertenece a Anthropic ni a Google. Los que más se acercan a "empresa establecida" son Supabase, PaddleOCR (PaddlePaddle), Onyx, Paperclip y Hallmark (autor de Together AI).
- Los clones con nombres como "anthropics-skills" en otras cuentas son copias de terceros. Usa siempre `github.com/anthropics/skills`.

---

## 4. Seguridad: escala de notas

Usé esta escala para todos los repositorios. **Es mi criterio, basado solo en lo visible en el README y la estructura del repo.**

| Nota | Significado |
|---|---|
| **A** | Publicador oficial verificado (Anthropic, Google, Microsoft, OpenAI, AWS, LangChain). Aun así se revisa lo que ejecuta. |
| **B** | Dueño identificable o empresa conocida, licencia abierta, actividad alta, y el contenido principal es texto o código sin señales de alarma en el README. |
| **B−** | Como B, pero con algo que revisar antes de confiar: scripts sin inspeccionar, instalación global, proyecto pequeño o sin historial, o implicaciones legales. |
| **C** | Ejecuta código automáticamente (hooks activos, `curl \| bash`, `npx` sin versión fija, descargas en ejecución), manda tus datos a terceros, o tiene una señal rara (dueño que no coincide, estrellas anómalas, proyecto muy nuevo). Úsalo solo tras leer el código. |
| **D** | Maneja credenciales de sesión (cookies, perfiles de navegador), ejecuta código arbitrario o tiene capacidades ofensivas, o corre con permisos amplios por defecto. **Solo en entorno aislado y con cuentas secundarias.** |

---

## 5. Agente orquestador: ¿desarrollar uno propio o partir de uno existente?

### 5.1 Tres cosas distintas que se llaman "orquestador"

1. **Gestores de sesiones de agentes de código.** Lanzan varios Claude Code/Codex/Gemini CLI en paralelo, cada uno en su rama (worktree), y te dejan revisar y fusionar. Ejemplos: claude-squad, bernstein, awslabs/cli-agent-orchestrator, scion.
2. **Frameworks/SDK para programar tu propio orquestador.** Son bibliotecas: tú escribes cómo se reparte el trabajo. Ejemplos: Claude Agent SDK, OpenAI Agents SDK, LangGraph, CrewAI.
3. **Plataformas de agentes "tipo empresa" o asistentes personales.** Agentes que viven permanentemente, reciben tareas, tienen presupuesto y equipo. Ejemplos: Paperclip, OpenClaw, Hermes Agent.

Estas tres no son intercambiables. Elegir mal la categoría es el error más común.

### 5.2 Panorama verificado

| Proyecto | Tipo | Dueño | Licencia | ⭐ | Notas |
|---|---|---|---|---|---|
| [Claude Agent SDK (Python)](https://github.com/anthropics/claude-agent-sdk-python) | SDK | Anthropic | MIT | 8.5k | Incluye `query()`, clientes con sesión, **hooks** que pueden denegar comandos, herramientas MCP propias. Trae el CLI de Claude Code dentro. Ojo: `allowed_tools` **auto-aprueba**, no restringe; para bloquear hay que usar `disallowed_tools`. Sus propios hooks de ejemplo son una "barrera", no un límite de seguridad. |
| [OpenAI Agents SDK](https://github.com/openai/openai-agents-python) | SDK | OpenAI | MIT | 30.7k | Traspasos (handoffs), guardrails, trazas. Declara ser agnóstico de proveedor. |
| [LangGraph](https://github.com/langchain-ai/langgraph) | Framework | LangChain | MIT | 44.9k | Ejecución duradera (se reanuda tras fallos), supervisión humana, memoria. Es "de bajo nivel". |
| [CrewAI](https://github.com/crewAIInc/crewAI) | Framework | CrewAI Inc | (no abrí) | — | Agentes por roles; mencionado en la lista awesome. No lo verifiqué directamente. |
| [claude-squad](https://github.com/smtg-ai/claude-squad) | Gestor de sesiones | org `smtg-ai` | AGPL-3.0 | 8.8k | tmux + worktrees. Instalador `curl \| bash` (hay Homebrew). Tiene `--autoyes` experimental que acepta todo. |
| [bernstein](https://github.com/sipyourdrink-ltd/bernstein) | Gestor de sesiones | 1 mantenedor | Apache-2.0 | 1.2k | Beta; coordinación determinista sin LLM; varias funciones declaradas "aún no publicadas". |
| [awslabs/cli-agent-orchestrator](https://github.com/awslabs/cli-agent-orchestrator) | Gestor (supervisor y trabajadores) | AWS Labs | Apache-2.0 | 1.6k | tmux + MCP; soporta Claude Code, Codex, Kiro y otros. |
| [GoogleCloudPlatform/scion](https://github.com/GoogleCloudPlatform/scion) | Plataforma con contenedores | Google Cloud | Apache-2.0 | 2.1k | Agentes en contenedores aislados (Docker, Podman, Kubernetes, Cloud Run). Pre-1.0 y no soportado oficialmente. |
| [Paperclip](https://github.com/paperclipai/paperclip) | Plataforma "empresa de agentes" | Paperclip Labs | MIT | 113.7k | Presupuestos, aprobaciones, roles, registro de actividad. Ver seguridad en sección 8. |
| [awesome-agent-orchestrators](https://github.com/andyrewlee/awesome-agent-orchestrators) | Lista curada | andyrewlee | CC0 | 2.4k | Catálogo de más de 300 proyectos por categoría. Es un directorio, no una validación de seguridad. |

Fuentes secundarias consultadas: Augment Code ("9 Open-Source Agent Orchestrators for AI Coding"), blogs comparativos 2026 (Morph, Requesty, Dupple). Sus cifras de estrellas y benchmarks **se contradicen entre sí** (por ejemplo, CrewAI aparece con 19k y con 52k), así que no las uso para decidir.

Dato de contexto: AutoGen quedó en modo mantenimiento y su sucesor es Microsoft Agent Framework, según blogs que cito en las fuentes (no lo verifiqué en el repo).

### 5.3 Mi recomendación: ¿propio o existente?

Esto es **mi juicio**, no un hecho de las fuentes. Se basa en que preguntas conceptos básicos (así que conviene minimizar la superficie que debes mantener y auditar) y en que este campo cambia cada semana.

**Recomiendo partir con piezas existentes y construir una capa propia delgada, en este orden:**

**Fase 0 (semanas 1-2): sin orquestador.** Usa Claude Code con subagentes y las skills oficiales de `anthropics/skills`, extraídas con el método de la sección 2.1. Objetivo: descubrir qué flujos de trabajo repites de verdad. Un orquestador sin flujos claros solo añade complejidad.

**Fase 1 (semanas 3-6): un gestor de sesiones existente, probado en pequeño.**
- Candidato 1: **awslabs/cli-agent-orchestrator** (dueño AWS Labs, Apache-2.0, política de seguridad documentada).
- Candidato 2: **claude-squad** (más popular y simple; instálalo por Homebrew y **no uses `--autoyes`**).
- Evita como primera elección proyectos de un solo mantenedor en beta (bernstein) o plataformas de gran superficie (OpenClaw, Paperclip) hasta entender lo básico.

**Fase 2 (cuando tengas flujos claros): tu capa propia sobre un SDK oficial.** Construye un orquestador pequeño con el **Claude Agent SDK**. Ventajas: (1) dueño oficial, (2) los **hooks** te dan un punto único para aprobar, denegar y registrar cada acción, (3) es la misma base que Claude Code. Si necesitas varios proveedores de modelos o ejecución que sobreviva a caídas, evalúa LangGraph.

**Por qué no construir desde cero ahora:**
- Los gestores existentes ya resolvieron aislamiento con worktrees, y ese es el problema aburrido y fácil de hacer mal.
- Cada orquestador propio es código tuyo que debes mantener y asegurar.
- No sabes todavía qué necesitas, y eso decide todo.

**Por qué sí construir tu propia capa más adelante:**
- Tu control de permisos y presupuesto queda en tu código, no en el de un tercero.
- Tus "carriles" de navegador (sección 6) y tus reglas de seguridad son específicos de ti.
- Puedes empezar con ~200 líneas: un bucle que reparte tareas, un máximo de 6 trabajos simultáneos, un registro de cada acción y un tope de gasto.

**Reglas mínimas para tu orquestador, sea propio o de terceros:**
1. Un tope de gasto y de pasos por tarea.
2. Cada agente trabaja en su propio worktree o contenedor, nunca en tu carpeta principal.
3. Registro de todas las acciones (qué comando, cuándo, resultado).
4. Aprobación humana para acciones irreversibles (borrar, pagar, publicar, enviar mensajes).
5. Sin credenciales reales dentro de los entornos de los agentes.
6. Los resultados de herramientas y páginas web se tratan como **datos no confiables**, nunca como órdenes.

---

## 6. Playwright headless en 6 carriles y computer use

### 6.1 Playwright MCP oficial: qué hace de fábrica (✔ verificado en README)

Repo: [microsoft/playwright-mcp](https://github.com/microsoft/playwright-mcp) (Microsoft, Apache-2.0).

- `--headless` corre sin ventana (por defecto es con ventana). Equivale a la variable `PLAYWRIGHT_MCP_HEADLESS`.
- `--isolated` mantiene el perfil solo en memoria; al cerrar, se pierde el estado.
- Un perfil persistente **solo lo puede usar un navegador a la vez**. Si varios clientes comparten carpeta, chocan. El README recomienda `--isolated` o un `--user-data-dir` distinto por cliente paralelo.
- `--shared-browser-context` hace lo contrario: reutiliza un único contexto entre clientes (no sirve para aislar).
- `--port` activa transporte HTTP.
- **El README dice textualmente que "Playwright MCP no es un límite de seguridad".** `--allowed-origins` y `--blocked-origins` tampoco lo son. `browser_run_code_unsafe` ejecuta JavaScript arbitrario y se describe como equivalente a ejecución remota de código (RCE).
- El acceso a archivos se limita por defecto al espacio de trabajo y `file://` está bloqueado salvo `--allow-unrestricted-file-access`.

### 6.2 ¿Cómo lograr 6 carriles?

Hay dos caminos. El primero es el que recomiendo para empezar.

**Camino A: 6 instancias del servidor oficial (simple).** Lanza 6 procesos de Playwright MCP, cada uno con `--headless --isolated`, y asigna uno a cada agente. Cada proceso tiene su navegador y su perfil en memoria, así que no se pisan. Ejemplo de configuración de un solo carril (repítelo con otro nombre por carril; fija una versión que hayas revisado en vez de `@latest`):

```json
{
  "mcpServers": {
    "pw-lane-1": {
      "command": "npx",
      "args": ["@playwright/mcp@<VERSION_REVISADA>", "--headless", "--isolated"]
    }
  }
}
```

**Camino B: tu propio código con la biblioteca Playwright (más control).** Un solo navegador, 6 contextos (`browser.newContext()`), cada uno con cookies, almacenamiento y caché propios, y un semáforo que limite a 6 tareas simultáneas. Es más liviano en memoria que 6 navegadores. Es la base natural si construyes tu propio orquestador en la Fase 2.

**Niveles de aislamiento (de menor a mayor):**
1. Contextos del mismo navegador: cookies separadas, mismo proceso.
2. Procesos separados (Camino A).
3. Contenedores o máquinas virtuales por carril: es el más fuerte, y es lo que Anthropic recomienda para computer use.

**Cuánta memoria necesitas:** cada Chromium consume bastante RAM y varía según la página. Mide con tu caso real antes de fijar 6 como número.

**Endurecimiento mínimo por carril:**
- Una lista de dominios permitidos y salida de red filtrada **fuera** del navegador (el filtro interno no es un límite de seguridad).
- Ninguna cuenta ni credencial real dentro del carril.
- Desactiva o no expongas la ejecución de JavaScript arbitrario si no la necesitas.
- Carpeta de subida de archivos restringida.
- Trata el contenido de las páginas como no confiable (inyección de instrucciones).
- No expongas el puerto HTTP a la red.

### 6.3 Envoltorios de terceros para paralelismo: evitar por ahora

Aparecieron proyectos que prometen sesiones paralelas sobre Playwright MCP. Los leí:

| Proyecto | ⭐ | Hallazgos | Nota |
|---|---|---|---|
| [dgutierrez1/concurrent-playwright-mcp](https://github.com/dgutierrez1/concurrent-playwright-mcp) | 5 | MIT, 1 mantenedor. Un contexto por sesión, headless por defecto, tope de 50 sesiones. Su modo HTTP **no tiene autenticación** (lo dice el README), por defecto permite navegar a cualquier URL, incluidas redes internas, y sube cualquier archivo local. | **C** |
| [sumyapp/playwright-parallel-mcp](https://github.com/sumyapp/playwright-parallel-mcp) | 19 | MIT. Un proceso por sesión, tope 10. `MCP_BACKEND` acepta **cualquier paquete npm** y los descarga como `@latest`: ejecución arbitraria y sin versión fija. | **D** |

Con 5 y 19 estrellas no tienen comunidad que los revise. El camino A usa el producto oficial y logra lo mismo.

### 6.4 Computer use

**Referencia oficial:** [anthropics/claude-quickstarts](https://github.com/anthropics/claude-quickstarts) (carpeta `computer-use-demo`, MIT) y la documentación en platform.claude.com. Es un entorno contenerizado para que Claude controle un escritorio.

**Advertencias oficiales (✔ leídas del README del demo):**
1. Usa una **máquina virtual o contenedor dedicado con privilegios mínimos**.
2. **No le des acceso a datos sensibles**, como inicios de sesión de cuentas.
3. **Limita el acceso a internet a una lista de dominios permitidos.**
4. **Pide confirmación humana** para decisiones con consecuencias reales (aceptar cookies, pagos, términos de servicio).
5. **Prompt injection:** Claude puede seguir órdenes que encuentre en páginas o imágenes aunque contradigan tu instrucción.
6. Es una función **beta** con riesgos propios, mayores cuando navega por internet.
7. **Arquitectura débilmente separada:** el bucle del agente corre dentro del mismo contenedor que Claude controla, y solo admite una sesión a la vez; hay que reiniciarlo entre sesiones.

La última es clave para ti: el demo oficial **no sirve como 6 carriles**. Para carriles paralelos con computer use necesitas un contenedor por carril.

**Alternativas de código abierto que encontré:**

| Proyecto | Dueño | ⭐ | Licencia | Notas |
|---|---|---|---|---|
| [browser-use](https://github.com/browser-use/browser-use) | org `browser-use` (empresa) | 121k | MIT | ✔ Solo navegador. `Browser()` por defecto usa un navegador **en la nube** de Browser Use y necesita una clave; `Browser('chromium')` es local con perfil temporal; `Browser('chrome')` **se conecta a tu Chrome abierto y a tus sesiones**. Abre un puerto de depuración en 127.0.0.1 sin autenticación (otros usuarios locales podrían conectarse). Su README dice que `sensitive_data` y las restricciones de dominio **no son un sandbox**. Nota **B** (con esas advertencias). |
| UI-TARS, Agent S3, CUA, Stagehand, Skyvern, Agent-E, OpenAdapt | varios | — | — | Solo aparecen en una comparación de Turing Post. **No los verifiqué.** Revisa licencia, mantenimiento y dueño antes de considerarlos. |

---

## 7. Catálogo A: oficiales

(Ya detallados en las secciones 3 y 5. Resumen de seguridad.)

| Repo | Enlace | Nota | Por qué |
|---|---|---|---|
| anthropics/skills | https://github.com/anthropics/skills | **A** | Oficial. Dice ser para demostración; incluye skills de documentos con scripts. Revisa cada skill antes de usarla. |
| anthropics/claude-quickstarts | https://github.com/anthropics/claude-quickstarts | **A** | Oficial. Computer use exige contenedor aislado. |
| anthropics/claude-agent-sdk-python | https://github.com/anthropics/claude-agent-sdk-python | **A** | Oficial. Tiene todas las herramientas por defecto (Bash, Write...); restringe tú los permisos. El CLI se descarga en la compilación del paquete. |
| google/skills | https://github.com/google/skills | **A** | Oficial. Tiene política de seguridad. Se instala con `npx skills add` (ejecuta código de npm). Prefiere extraer. |
| microsoft/playwright-mcp | https://github.com/microsoft/playwright-mcp | **A** | Oficial; ejecuta un navegador y JavaScript. Su README advierte que no es un límite de seguridad. |
| openai/openai-agents-python | https://github.com/openai/openai-agents-python | **A** | Oficial. |
| awslabs/cli-agent-orchestrator | https://github.com/awslabs/cli-agent-orchestrator | **A** | Org de AWS Labs. Instalación desde `main` con `uv tool install git+...@main`: usa una versión etiquetada. |
| GoogleCloudPlatform/scion | https://github.com/GoogleCloudPlatform/scion | **B** | Dueño oficial, pero declara "no soportado oficialmente" y pre-1.0. Por eso lo pongo en B. |
| langchain-ai/langgraph | https://github.com/langchain-ai/langgraph | **A** | Empresa conocida (LangChain Inc.). |

---

## 8. Catálogo B: los 18 repositorios de tu primera consulta (alfabético)

Cada ficha tiene: enlace (✔ = lo abrí en GitHub), publicador, licencia, estrellas, qué hace, instalación, nota y por qué. **Fuente de la información:** página del repositorio en GitHub, más las capturas que enviaste.

### 1. AI Job Search
- **Enlace:** https://github.com/MadsLorentzen/ai-job-search ✔ (GitHub redirigió a `ai-job-search-dev/ai-job-search`; el README acredita a Mads Lorentzen)
- **Publicador:** org `ai-job-search-dev` (autor Mads Lorentzen). **No afiliado a Anthropic** (lo dice el README). **Licencia:** MIT. **⭐:** 71.6k.
- **Qué hace:** comandos de Claude Code (`/setup`, `/scrape`, `/apply`, `/rank`, `/interview`...) para evaluar ofertas, adaptar CV en LaTeX, escribir cartas y llevar el seguimiento.
- **Instalación:** clonar y ejecutar `python3 tools/install_portal_clis.py` (corre `bun install --ignore-scripts`). Requiere Claude Code de pago, Python, Bun y LaTeX.
- **Nota: C.** **Por qué:** (1) `/setup` escribe tus datos personales (nombre, contacto, historial, expectativas de sueldo) en archivos versionados, y **un fork de un repo público es siempre público**, así que puedes publicar tus datos por accidente; (2) `.claude/settings.json` **pre-aprueba** que unos CLI de portales corran sin preguntar, y vienen de forks de terceros; (3) las ofertas de empleo son contenido no confiable (inyección de instrucciones) y las defensas son solo instrucciones, no un sandbox; (4) el propio README dice que automatizar LinkedIn viola sus términos.
- **Ojo:** hay proyectos parecidos (santifer/career-ops, suraj-davariya/ai-job-search). Confirma que es el que viste.

### 2. Book to Skill
- **Enlace:** https://github.com/virgiliojr94/book-to-skill ✔ (el propio post lo muestra)
- **Publicador:** cuenta personal `virgiliojr94`. **MIT.** **⭐:** 37.8k. v1.4.0 según tu captura.
- **Qué hace:** convierte libros y documentos (PDF, EPUB, DOCX, MD, HTML, RTF, MOBI) en una skill que carga solo el capítulo necesario.
- **Instalación:** `npx skills add virgiliojr94/book-to-skill` o clonar a la carpeta de skills.
- **Nota: B−.** **Por qué:** trae `scripts/extract.py` y herramientas que no inspeccioné; escribe en carpetas de tu usuario (`~/.agents/skills/`, intenta un symlink a `~/.claude/skills/`); puede **publicar la skill generada en GitHub** (privado por defecto); el texto del libro se envía al modelo que uses. La cifra "24×–51× menos tokens" es del autor.

### 3. Diagram Design
- **Enlace:** https://github.com/cathrynlavery/diagram-design ✔
- **Publicador:** Cathryn Lavery (cuenta personal; su empresa LittleMight lista el plugin). **MIT.** **⭐:** 66.9k.
- **Qué hace:** skill que genera un diagrama en un único HTML+SVG (45 o 50 tipos; la página se contradice) y reimporta draw.io, Mermaid, Excalidraw y PlantUML.
- **Instalación:** `npx skills add cathrynlavery/diagram-design` o marketplace de plugins, o copia manual.
- **Nota: B−.** **Por qué:** scripts de Python en `skills/diagram-design/scripts/` (no inspeccionados); exportar PNG requiere Playwright y Chromium; su README advierte que **"las versiones oficiales solo vienen de este repositorio"** y que existen copias no oficiales; la única petición de red por defecto es Google Fonts.

### 4. Hallmark
- **Enlace:** https://github.com/Nutlope/hallmark ✔
- **Publicador:** Nutlope (el README dice "Made by Together AI"). **MIT.** **⭐:** 54.5k.
- **Qué hace:** skill de diseño "anti AI-slop" con unos cincuenta temas visuales, un linter de resultados y una biblioteca de 755 fotos de Wikimedia Commons.
- **Instalación:** `npx skills add nutlope/hallmark -g` (global), plugin de Claude Code, o `node skills/hallmark/scripts/install.mjs`.
- **Nota: B−.** **Por qué:** `install-hook.mjs` **modifica la configuración de Claude Code** para añadir una verificación al editar; `-g` instala para todos tus proyectos; la verificación en navegador necesita `puppeteer-core` global y Chrome. No vi claves de API requeridas. El nombre de la infografía (nutlope/hallmark) coincide con el repo.

### 5. Humanizer
- **Enlace:** https://github.com/blader/humanizer ✔
- **Publicador:** cuenta personal `blader` (patrocinio de cfo.ai declarado). **MIT.** **⭐:** 68.8k.
- **Qué hace:** skill que reescribe texto generado por IA para que suene más humano. El README dice que **no** intenta engañar a detectores.
- **Instalación:** marketplace de plugins (Claude Code ≥ 2.1.142), `npx skills add`, o copiar `SKILL.md`.
- **Nota: B−.** **Por qué:** el repo tiene carpetas `scripts` y `agents` que no pude abrir; edita los archivos que le indiques. Para minimizar riesgo, **extrae solo `SKILL.md`**.

### 6. IFixAi
- **Enlace:** https://github.com/ifixai-ai/ifixai ✔
- **Publicador:** org `ifixai-ai` (empresa iFixAi Inc., Delaware, según un review). **Apache-2.0.** **⭐:** 45.2k.
- **Qué hace:** audita agentes de IA (errores, puntos ciegos, alineación) con inspecciones, jueces independientes e informes con nota. Fue "Producto #1 del día" en Product Hunt.
- **Instalación:** `pip install ifixai`, plugin de Claude Code o Codex, o `uvx ifixai install --agents ...`.
- **Nota: C.** **Por qué:** las pruebas **son peticiones reales** a tu agente (usa una copia de pruebas); **las respuestas de tu agente se envían a un modelo "juez"** de otro proveedor; el CLI recibe el token de tu agente como parámetro (queda en el historial de la terminal); relación estrellas/forks alta; "independiente" y "en menos de 120 segundos" son afirmaciones propias sin respaldo en la página.

### 7. Impeccable
- **Enlace:** https://github.com/pbakaus/impeccable ✔
- **Publicador:** Paul Bakaus (cuenta personal; parte de la skill `frontend-design` de Anthropic). **Apache-2.0.** **⭐:** 109.6k.
- **Qué hace:** conjunto de comandos de diseño (`polish`, `bolder`, `critique`, `audit`) y un detector de "AI slop" con 62 reglas, sin LLM ni clave.
- **Instalación:** `npx impeccable install` (y otras vías).
- **Nota: C.** **Por qué (todo está en su propio README):** instala **hooks que corren en cada edición, incluso si denegaste comandos al modelo**; descarga un binario ("engine") a `~/.impeccable/bin/` la primera vez; el modo "live → Apply" puede lanzar `claude` o `codex` con **banderas que desactivan aprobaciones y sandbox**; puede ejecutar un script de `package.json` del proyecto; la telemetría anónima se puede desactivar, pero la consulta a su catálogo no. Extrae solo lo que necesitas y no uses el modo live.

### 8. Last 30 Days
- **Enlace:** https://github.com/mvanhorn/last30days-skill ✔ (**corrección:** tu captura decía `mwanhorn`, que da error 404; el dueño real es `mvanhorn`)
- **Publicador:** cuenta personal `mvanhorn`. **MIT.** **⭐:** 67.6k.
- **Qué hace:** busca un tema en Reddit, X, YouTube, TikTok, Hacker News, Polymarket, GitHub, arXiv y la web, y redacta un informe con citas.
- **Instalación:** plugin, `npx skills add`, `.skill` para Claude.ai, `.mcpb` para Claude Desktop, o symlink. Requiere Python 3.12+.
- **Nota: D.** **Por qué:** **puede leer cookies de tu navegador** para X y manejar sesiones locales (Xiaohongshu); usa servicios de scraping de terceros (posible choque con los términos de las plataformas); incluye `setup-keychain.sh` que lee y escribe en el llavero de macOS; en el primer uso **instala CLI automáticamente**; el plugin **se actualiza solo**, así que el código cambia sin que lo revises; envía contenido a proveedores de LLM si configuras claves. Úsalo solo en entorno aislado y con cuentas secundarias.

### 9. Onyx
- **Enlace:** https://github.com/onyx-dot-app/onyx ✔
- **Publicador:** org `onyx-dot-app` (empresa). **MIT** (edición comunitaria; la Enterprise tiene términos aparte). **⭐:** 33.4k.
- **Qué hace:** plataforma de chat con IA autoalojada, conectada a tus fuentes de conocimiento.
- **Instalación:** `curl -fsSL https://onyx.app/install_onyx.sh | bash`, o Docker, Kubernetes, Helm, Terraform.
- **Nota: B−.** **Por qué:** empresa identificable y política de seguridad; el instalador de una línea es `curl | bash` (descarga el script y revísalo antes, o usa Docker). **Ojo con tu infografía:** el enlace que mostraba junto a Onyx apuntaba a `senlindesign/taste-skill`, que no es Onyx; parece un error de la infografía.

### 10. openGym
- **Enlace:** https://github.com/DuarteSantos8/openGym ✔
- **Publicador:** cuenta personal `DuarteSantos8`. **AGPL-3.0** para el código; las imágenes de ejercicios tienen licencia aparte. **⭐:** 21.3k.
- **Qué hace:** seguidor de gimnasio y peso corporal autoalojado.
- **Instalación:** Docker Compose (`docker compose pull` y `up -d`), corre en `localhost:8080`.
- **Nota: B−.** **Por qué:** código abierto y auto-alojado; el README dice **sin telemetría** (no lo verifiqué en el código); datos en JSON local; funciones opcionales envían datos a donde las configures (entrenador de IA, webhooks); declara que se desarrolla en buena parte con Claude Code; hay un patrocinador comercial (ElfHosted). No puedo evaluar si las estrellas son orgánicas.

### 11. PaddleOCR
- **Enlace:** https://github.com/PaddlePaddle/PaddleOCR ✔
- **Publicador:** org `PaddlePaddle`. **Apache-2.0.** **⭐:** 92.7k.
- **Qué hace:** convierte PDF e imágenes en datos estructurados (facturas, contratos), con más de 100 idiomas.
- **Instalación:** paquete `paddleocr` por pip (la página remite a su documentación).
- **Nota: B.** **Por qué:** organización establecida, proyecto antiguo y con gran comunidad. Los modelos se descargan de AIStudio, ModelScope y Hugging Face. La carga de modelos con formatos que pueden ejecutar código (como pickle) **no aparece en la página**; verifícalo en su documentación.

### 12. Paperclip
- **Enlace:** https://github.com/paperclipai/paperclip ✔
- **Publicador:** org `paperclipai` (Paperclip Labs, Inc). **MIT.** **⭐:** 113.7k.
- **Qué hace:** plano de control para coordinar agentes de IA: objetivos, tareas, aprobaciones, presupuestos, gobernanza y registro de actividad.
- **Instalación:** `npx paperclipai@latest onboard --yes` (Node 24.11+), o clonar con pnpm, o Docker.
- **Nota: C.** **Por qué:** la **telemetría viene activada por defecto** (se puede apagar; dicen no recoger prompts ni secretos); el arranque rápido usa **modo local sin autenticación**; los topes de presupuesto **pueden demorar** frente al gasto real; los paquetes exportados pueden incluir valores de entorno y rutas locales; los agentes pueden usar tu identidad personal de GitHub; muchas funciones están marcadas como parciales o planificadas; 3.7k incidencias y 2.9k PR abiertos.

### 13. Ponytail
- **Enlace:** https://github.com/DietrichGebert/ponytail ✔
- **Publicador:** cuenta personal `DietrichGebert`. **MIT.** **⭐:** 190.4k.
- **Qué hace:** reduce el código que el agente escribe. Lo hace obligándolo a seguir una "escalera" antes de programar. **Tu pregunta:** sí, el efecto buscado es gastar menos, pero porque **escribe menos código**, no por comprimir respuestas. Su README afirma ~54% menos código, ~20% más barato y ~27% más rápido; son pruebas del propio autor, y una incidencia cuestiona la comparación.
- **Instalación:** `/plugin marketplace add DietrichGebert/ponytail` y `/plugin install ponytail@ponytail`; en Codex hay que confiar en dos hooks.
- **Nota: C.** **Por qué:** instala **hooks que corren cada sesión**; puede leer datos de sesiones pasadas de Claude Code con `/ponytail band import`; no vi telemetría en la página; el autor avisa que copias con `.exe`/`.dll` **no son suyas**; lenguaje promocional con afirmaciones no verificables ("cero bugs, cero CVE"). Revisa `hooks/` antes de instalar o extrae solo `SKILL.md`.

### 14. REA
- **Enlace:** https://github.com/morluto/rea ✔ (tu captura decía `morutao/rea` en una y `morluto` en otra; el dueño real es **`morluto`**)
- **Publicador:** cuenta personal `morluto`. **MIT** (el código propio; terceros con sus licencias). Paquete npm `rea-agents`.
- **Qué hace:** un MCP que permite a tu agente hacer ingeniería inversa de binarios, apps Electron, APK, firmware y capturas de red. Puede **ejecutar el objetivo** (captura de procesos) y controlar un Android por adb.
- **Instalación:** `npx rea-agents setup`: te muestra los cambios, hace copia de seguridad de la configuración del agente y puede instalar Hopper con tu aprobación.
- **Nota: D.** **Por qué:** **modifica las configuraciones de tus agentes**; ejecutar muestras desconocidas con tus permisos es peligroso y **el README no advierte de malware**; las cifras (457.7k estrellas, 112.3k forks, 941 incidencias, 914 PR abiertos) son anormalmente altas y la sección de versiones salió vacía. Uso solo en una máquina virtual sin credenciales y solo sobre software que tengas derecho a analizar.

### 15. Scrapling
- **Enlace:** https://github.com/D4Vinci/Scrapling ✔
- **Publicador:** Karim Shoair (`D4Vinci`). **BSD-3-Clause.** **⭐:** 102.5k.
- **Qué hace:** biblioteca de scraping web con modo servidor MCP.
- **Instalación:** `pip install scrapling` (y extras `[fetchers]` o `[ai]` con `scrapling install` para navegadores).
- **Nota: B−.** **Por qué:** autor identificable y licencia abierta; el riesgo es **legal/éticas**: su característica destacada es **saltarse protecciones anti-bot** (Cloudflare, huellas digitales) y dice ayudar con "cualquier CAPTCHA"; el cumplimiento de robots.txt es opcional; muchos banners de patrocinadores (proxies, resolución de CAPTCHA). Úsalo respetando los términos de cada sitio.

### 16. Strix
- **Enlace:** https://github.com/usestrix/strix ✔
- **Publicador:** org `usestrix`. **Apache-2.0.** **⭐:** 75.4k.
- **Qué hace:** agentes de IA autónomos que prueban tu app como hackers: ejecutan tu código, validan vulnerabilidades con pruebas de concepto y sugieren arreglos.
- **Instalación:** `curl -sSL https://strix.ai/install | bash`, o `pipx`/`uv`/`pip` (`strix-agent`). Requiere Docker.
- **Nota: C (uso autorizado únicamente).** **Por qué:** es una herramienta **ofensiva** (proxy de interceptación, navegador, ejecución de comandos, exploits) que corre dentro de Docker; el README advierte que **actúa activamente contra los objetivos** y que solo se debe probar lo que sea tuyo o tengas permiso; instalador `curl | bash`; guarda tu clave de LLM en `~/.strix/cli-config.json`. Úsalo solo contra sistemas que posees.

### 17. Supabase
- **Enlace:** https://github.com/supabase/supabase ✔
- **Publicador:** org `supabase` (empresa). **Apache-2.0.** **⭐:** 115k.
- **Qué hace:** plataforma de desarrollo sobre Postgres: base de datos, autenticación, APIs automáticas, funciones, almacenamiento y herramientas de IA; alternativa abierta a Firebase.
- **Instalación:** servicio alojado o auto-alojado según su documentación.
- **Nota: B.** **Por qué:** empresa establecida y producto maduro con política de seguridad; no revisé el contenido de esa política. La cifra "USD 116M" del post no la verifiqué.

### 18. UI/UX Pro Max
- **Enlace:** https://github.com/nextlevelbuilder/ui-ux-pro-max-skill ✔
- **Publicador:** cuenta/org `nextlevelbuilder`. **MIT.** **⭐:** 149k.
- **Qué hace:** genera sistemas de diseño de interfaz (reglas, estilos, tipografías) para Claude y otros agentes.
- **Instalación:** `npm install -g ui-ux-pro-max-cli` o `npx ui-ux-pro-max-cli init --ai claude`, plugin o copia manual.
- **Nota: B−.** **Por qué:** el README dice que `search.py` usa solo la biblioteca estándar y **no hace llamadas de red** (no lo verifiqué); el CLI **escribe en carpetas globales**; instalación global de npm; usa un workflow de GitHub con secreto; promociona una versión Premium. Extrae solo los archivos de la skill.

---

## 9. Catálogo C: los repositorios de tu segunda consulta

### Agentes
**Hermes Agent**
- **Enlace:** https://github.com/NousResearch/hermes-agent ✔ — **Publicador:** Nous Research (org). **MIT.** **⭐:** 263.2k.
- **Qué es:** agente de IA de código abierto, autoalojado, que **aprende de la experiencia** (crea y refina skills), tiene memoria entre sesiones y se maneja desde terminal o Telegram, Discord, Slack, WhatsApp. No está atado a un modelo. El repo muestra 6 backends de ejecución: local, Docker, SSH, Modal, Daytona y Vercel Sandbox. **No es lo mismo que los modelos "Hermes" de la misma empresa.**
- **Instalación:** `curl ... install.sh | bash` en Linux/macOS; PowerShell equivalente en Windows.
- **Nota: C.** **Por qué:** dueño oficial identificable y política de seguridad con aprobación de comandos y aislamiento por contenedores; pero el instalador es `curl | bash`, y por defecto el backend "local" ejecuta con tu usuario: usa Docker.

**OpenClaw**
- **Enlace:** https://github.com/openclaw/openclaw ✔ — **Publicador:** org `openclaw` (administrada por la "OpenClaw Foundation", que el README describe como 501(c)(3) independiente). **MIT.** **⭐:** ~394k.
- **Qué es:** asistente personal de IA de código abierto que corre en tus dispositivos y se conecta a apps de mensajería. Lo creó Peter Steinberger (originalmente "Clawdbot").
- **Instalación:** script `curl`, PowerShell, `npm install openclaw@latest`, Docker, Nix.
- **Nota: D (en su configuración por defecto).** **Por qué:** las herramientas **corren en tu máquina** y el aislamiento está **desactivado por defecto**; recibe mensajes de terceros que trata como entrada no confiable; su pestaña de seguridad lista 722 elementos; el README pide leer la guía de seguridad antes de exponerlo. Si lo usas, activa el sandbox y no lo expongas a internet.

### Skills y herramientas de creación
**/brag**
- **Enlace:** https://github.com/latent-spaces/brag ✔ — org `latent-spaces`. **MIT.** **⭐:** 21.4k.
- **Qué es:** skill que lee tu proyecto y genera un video de lanzamiento de ~20 s con música, movimiento y texto para publicar. Se renderiza con Hyperframes (de HeyGen). Hay variante `/brag-slim`.
- **Instalación:** plugin, `npx skills add ...`, copia con rsync, o servicio alojado letsbrag.app.
- **Nota: C.** **Por qué:** requiere `npx hyperframes` (descarga paquetes sin versión fija); tiene carpeta `scripts/`; el agente ejecuta renderizados en tu máquina; el servicio alojado implica enviar tu proyecto a un tercero.

**/voicestudio (VoiceStudio / OmniVoice Studio)**
- **Enlace:** https://github.com/debpalash/VoiceStudio ✔ (mi búsqueda lo mostró como `OmniVoice-Studio`; hay forks con nombres parecidos, usa el de `debpalash`) — **AGPL-3.0** (otras fuentes citan FSL-1.1; revisa el LICENSE). **⭐:** ~110k.
- **Qué es:** alternativa local a ElevenLabs: clona voces, diseña voces, dobla video y dicta. Declara 646 idiomas (cifra de su marketing).
- **Instalación:** múltiples: `curl | sh` desde `voicestudio.sh`, Homebrew, Snap, Docker, APK/IPA por sideload...
- **Nota: C.** **Por qué:** muchos canales de instalación (incluidos instaladores de línea de comandos y APK/IPA fuera de las tiendas); los pesos de los modelos tienen licencias propias; la **clonación de voz** exige consentimiento del dueño, y la app local **no lo verifica técnicamente**; hay un producto Pro cerrado.

**/logodesignskill**
- **No hay un repo único con ese nombre.** Opciones que encontré: [neonwatty/logo-designer-skill](https://github.com/neonwatty/logo-designer-skill) ✔ (MIT, 201 ⭐, logos SVG iterativos), [nexu-io/open-design](https://github.com/nexu-io/open-design) (skill `brandkit`, no lo abrí), [whawkinsiv/solo-founder-superpowers](https://github.com/whawkinsiv/solo-founder-superpowers) (skill `brand`, no lo abrí), op7418/logo-generator-skill, rampstackco/brand-identity.
- **Nota neonwatty: B−.** Proyecto pequeño, MIT; tiene `scripts/` y `package.json` que no inspeccioné.
- **Nota para los demás:** no verificados; trata como **C** hasta revisarlos.

**SupoClip**
- **Enlace:** https://github.com/FujiwaraChoki/supoclip ✔ — usuario `FujiwaraChoki`. **AGPL-3.0.** **⭐:** ~3.0k.
- **Qué es:** convierte videos largos en clips verticales: transcribe, puntúa los mejores momentos, recorta y agrega subtítulos animados. (Duración la configuras; no encontré un valor fijo.)
- **Instalación:** Docker Compose con un archivo `.env` con tus claves.
- **Nota: B−.** **Por qué:** código abierto en Docker; **envía audio a AssemblyAI y transcripciones a tu proveedor de LLM** (con Ollama local queda en tu máquina); descarga videos de YouTube/Twitch (ojo con derechos y términos); un solo desarrollador.

**PhotoCraft / ArtCraft ("Adobe clonado")**
- **Enlaces:** https://github.com/storytold/photocraft ✔ y https://github.com/storytold/artcraft ✔ — cuenta `storytold` ("ArtCraft Team"). **MIT o Apache-2.0, a elección.** **⭐:** 79.2k (photocraft) y 40.9k (artcraft).
- **Qué es:** reimplementaciones "clean-room" de Adobe en Rust: PhotoCraft (≈Photoshop), y otras apps de la suite ArtCraft. Tu captura decía "trece apps"; las fuentes que leí decían siete. Puede haber crecido: verifícalo. Dice estar en **alfa temprana** y no afiliado a Adobe. Una fuente afirma que se escribió con ayuda de Claude.
- **Instalación:** binarios en GitHub Releases o `cargo run --release`. **Windows firmado; macOS firmado y notarizado; Linux y FreeBSD sin firma** (trae `SHA256SUMS.txt`).
- **Nota: C.** **Por qué:** proyecto muy nuevo y en alfa; ArtCraft envía prompts y archivos a 62 modelos en la nube de terceros y **no hay política de privacidad visible**; las estrellas son enormes para un proyecto tan joven; riesgo de patentes de Adobe sin aclarar. Verifica el checksum de lo que descargues.

**ayghri/i-have-adhd** (**tu pregunta: ¿es para respuestas más cortas?**)
- **Enlace:** https://github.com/ayghri/i-have-adhd ✔ — usuario `ayghri` (Ayoub Ghriss). **MIT.** **⭐:** 61.1k.
- **Qué es:** una skill con 10 reglas de comunicación: respuesta primero, pasos numerados, sin relleno. **Sí, hace las respuestas más concisas y ordenadas, pero no es una función "para TDAH"**: cambia cómo presenta Claude la información, no lo que sabe.
- **Instalación:** marketplace de plugins o un prompt que le pide al agente seguir el `AGENTS.md` del repo.
- **Nota: C.** **Por qué:** pese a ser solo estilo, el repo contiene `hooks/`, `scripts/`, `evals/` y `extensions/` que no vi por dentro; el método de instalación por prompt **le pide al agente que siga instrucciones de un archivo remoto**. Extrae solo `SKILL.md`.

**calesthio/OpenMontage**
- **Enlace:** https://github.com/calesthio/OpenMontage ✔ — **la página resolvió bajo la org `OpenMontageInc`** mientras los enlaces de clonación apuntaban a `calesthio`. **AGPL-3.0.** **⭐:** ~80.3k.
- **Qué es:** sistema de producción de video con agentes: investigación, guion, voz y edición, ejecutado desde Claude Code, Copilot o Cursor. Tiene muchos pipelines y herramientas (las cifras varían por fuente).
- **Instalación:** `make setup` (necesita Python 3.10+, Node.js, FFmpeg).
- **Nota: C.** **Por qué:** **el dueño no coincide** entre la página y los enlaces (verifica cuál es el oficial); el agente **instala paquetes y llama APIs de pago** con tus claves (hay topes de presupuesto por defecto de 10 USD y aprobación por acción, sin verificar su cumplimiento); contiene texto dirigido a "agentes OpenClaw" dentro del README; el instructivo manda actualizar certificados del sistema por un enlace externo; las estrellas son muy altas para un proyecto que aún se describe "tomando forma".

**Agent-Reach (tu "iqjiy" / "claude/agent-reach")**
- **Enlace:** https://github.com/Panniantong/Agent-Reach ✔ — usuario `Panniantong`. **MIT.** **⭐:** ~111.6k. No encontré ningún repositorio `iqjiy`.
- **Qué es:** CLI que le da a tu agente acceso a Bilibili, V2EX, GitHub, YouTube y la web general; opcionales XiaoHongShu, Xueqiu, BOSS, LinkedIn, Reddit; experimentales X, Facebook e Instagram. **El número es 13, 14 o 16 según la fuente; no hay 13 exactos confirmados.**
- **Instalación:** le pides a tu agente que siga un `install.md` remoto; instala `uv`.
- **Nota: D.** **Por qué:** para sitios con login, **copia tu sesión de Chrome** de ese sitio a un perfil separado (dicen no leer tu gestor de contraseñas); automatizar sesiones en X, Reddit o Instagram probablemente **infringe sus términos**; la instalación por "agente que ejecuta un archivo remoto" es una superficie de confianza amplia; el README tiene anuncios de patrocinadores y advierte de estafas con tokens falsos; el paquete `agent-reach` de PyPI **no es** este proyecto. "Sin costo de API" no significa sin configuración ni sin riesgo.

**abi/screenshot-to-code**
- **Enlace:** https://github.com/abi/screenshot-to-code ✔ — usuario `abi`. **MIT.** **⭐:** 81.2k.
- **Qué es:** convierte capturas, maquetas, Figma y grabaciones en HTML/Tailwind, React o Vue. Es lo que se usa para "copiar sitios".
- **Instalación:** backend con Poetry y frontend con pnpm, o Docker; requiere claves de OpenAI, Anthropic o Gemini.
- **Nota: B.** **Por qué:** proyecto antiguo, MIT y muy usado; el README no dice dónde se guardan las claves del diálogo de ajustes; ejemplos que replican NYTimes e Instagram: **copiar sitios ajenos puede violar derechos de autor y marcas**.

**taste-skill (senlindesign)** — apareció en tu infografía junto a Onyx
- **Enlace:** https://github.com/senlindesign/taste-skill ✔ — usuario `senlindesign`. **MIT.** **⭐:** 468.
- **Qué es:** skill `/taste <url>` que analiza el diseño visual de un sitio con Playwright y exporta reglas a Cursor, Windsurf y CLAUDE.md.
- **Nota: C.** **Por qué:** instala Playwright MCP con `npx @playwright/mcp@latest` (sin versión fija); inyecta `extract.js` en páginas que visita; **agrega contenido a CLAUDE.md** y otros archivos de reglas de tus proyectos; proyecto pequeño sin versiones publicadas.

---

## 10. Catálogo D: orquestadores y herramientas de carriles

(Ver sección 5 para el panorama y la tabla de dueños. Fichas de seguridad.)

| Proyecto | Enlace | Nota | Por qué |
|---|---|---|---|
| claude-squad | https://github.com/smtg-ai/claude-squad ✔ | **C** | AGPL-3.0; instalador `curl \| bash` (hay Homebrew); `--autoyes` experimental acepta todas las confirmaciones de los agentes. |
| bernstein | https://github.com/sipyourdrink-ltd/bernstein ✔ | **C** | Beta, un solo mantenedor, funciones "aún no publicadas"; sus garantías de determinismo son autodescritas. Fija versión. |
| awslabs/cli-agent-orchestrator | https://github.com/awslabs/cli-agent-orchestrator ✔ | **A** | Org oficial AWS Labs. |
| scion | https://github.com/GoogleCloudPlatform/scion ✔ | **B** | Org Google Cloud pero "no soportado oficialmente". |
| browser-use | https://github.com/browser-use/browser-use ✔ | **B** | Empresa; ver advertencias en 6.4. |
| concurrent-playwright-mcp | https://github.com/dgutierrez1/concurrent-playwright-mcp ✔ | **C** | 5 ⭐; HTTP sin autenticación. |
| playwright-parallel-mcp | https://github.com/sumyapp/playwright-parallel-mcp ✔ | **D** | Ejecuta cualquier paquete npm como backend `@latest`. |
| awesome-agent-orchestrators | https://github.com/andyrewlee/awesome-agent-orchestrators ✔ | **B** | Es una lista (CC0), no ejecuta nada; no valida los proyectos que lista. |

---

## 11. Catálogo E: páginas, servicios y funciones (sin repositorio)

**Fuente de todo este bloque:** búsquedas web (blogs, páginas del vendedor y agregadores). Son fuentes secundarias o del propio proveedor; no las contrasté con la documentación oficial salvo donde digo lo contrario.

| Tema | Qué es | Enlace | Seguridad / notas |
|---|---|---|---|
| **Linear** | Gestor de tareas e incidencias para equipos de software; rápido, con atajos de teclado e integración con GitHub. Alternativa a Asana o Trello. | https://linear.app | Servicio comercial; revisa permisos de las integraciones que conectes. |
| **Wispr Flow** | Dictado por voz con IA ("habla en vez de escribir"): quita muletillas, corrige gramática y adapta el tono por app. Mac, Windows, iPhone, Android. Plan gratis limitado. | https://wisprflow.ai | Pide **muchos permisos del dispositivo** y audio de tu voz; las reseñas de privacidad son mixtas. El vendedor declara SOC 2 e ISO 27001 (afirmación suya). |
| **Meta Muse (Muse Spark)** | Agente de Meta que puede leer lo que guardas en Instagram. Su demo oficial convierte un reel de receta guardado en lista de compras. | (anuncio de Meta; sin enlace único verificado) | Para dar acceso **le entregas tu cuenta de Instagram**. No encontré documentación oficial sobre límites del acceso a "guardados". |
| **OpenArt (explainer)** | Generador de videos explicativos desde un guion o frase, con escenas, voz y edición. Tiene "One-Click Story". | https://openart.ai/features/explainer-video-maker/ | Servicio de pago por créditos; verifica precios en el sitio oficial. |
| **Miro** | Pizarra colaborativa online. Su **servidor MCP oficial** conecta Claude con tus tableros: crear diagramas desde un PRD o código y generar código desde un tablero. | https://miro.com/marketplace/miro-mcp-for-claude/ | Hay dudas de usuarios sobre permisos poco definidos y límites en tableros grandes. Es servicio del proveedor. |
| **"Teach Claude a skill" / Record a skill** | Función de **Claude Cowork** (app de escritorio): grabas pantalla y narras una tarea y Claude la convierte en una skill reutilizable. Lanzada el 21-jul-2026 según la prensa. | (documentación oficial de Anthropic; confírmala allí) | La grabación captura todo lo que ves: **cierra pestañas y datos privados antes de grabar**. Las fuentes discrepan en planes y plataformas; hay un límite aproximado de ~10 min. |
| **Flujo "transcribe con timestamps → Google Omni"** | No encontré el flujo ni un producto de Google llamado "Omni". Sí existen skills de Claude basadas en Gemini que transcriben video con marcas de tiempo (por ejemplo `mrgoonie/ai-multimodal`, `omnicaptions`). | — | Sin verificar. Pásame el nombre exacto o el video y lo busco. |
| **Skill "pricing-strategy.md" (agshinrajabov)** | No encontré ningún repo de ese usuario ni ese archivo. Skills parecidas: `onewave-ai/claude-skills` (genera un `pricing-strategy.md`), `mfwarren/entrepreneur-claude-skills`, `ognjengt/founder-skills`, `alirezarezvani/claude-skills`. | — | Sin verificar. Si tienes el enlace, lo reviso. |
| **Skill "Apple design"** | Hay varias con ese nombre: `axiaoge2/apple-hig-designer`, `memi-design/design-skills`, `emilkowalski/skills`, `aiskillstore/marketplace`. | https://github.com/axiaoge2/apple-hig-designer | Ninguna es de Apple. Revisa el autor antes de usarlas y extrae solo el `SKILL.md`. |

---

## 12. Tabla maestra de seguridad (resumen)

| Repo | Dueño | Nota | Señal principal |
|---|---|---|---|
| anthropics/skills | Anthropic | A | Oficial; demostración |
| anthropics/claude-quickstarts (computer-use-demo) | Anthropic | A | Contenedor obligatorio |
| anthropics/claude-agent-sdk-python | Anthropic | A | Permisos amplios por defecto |
| google/skills | Google | A | Política de seguridad |
| microsoft/playwright-mcp | Microsoft | A | "No es un límite de seguridad" |
| openai/openai-agents-python | OpenAI | A | Oficial |
| awslabs/cli-agent-orchestrator | AWS Labs | A | Oficial |
| langchain-ai/langgraph | LangChain | A | Empresa |
| obra/superpowers | persona (obra) | B | Hooks de inicio de sesión; telemetría opcional |
| GoogleCloudPlatform/scion | Google Cloud | B | "No soportado oficialmente" |
| browser-use | empresa | B | Nube por defecto; puerto de depuración |
| PaddleOCR | PaddlePaddle | B | Revisar carga de modelos |
| Supabase | empresa | B | Maduro |
| screenshot-to-code | persona | B | Derechos de autor |
| Onyx | empresa | B− | `curl \| bash` |
| book-to-skill | persona | B− | Escribe en tu home; publica en GitHub |
| diagram-design | persona | B− | Scripts sin inspeccionar |
| Hallmark | persona (Together AI) | B− | Modifica config de Claude |
| Humanizer | persona | B− | Carpetas scripts/agents |
| openGym | persona | B− | Sin verificar telemetría |
| Scrapling | persona | B− | Anti-bot; legal |
| SupoClip | persona | B− | Datos a terceros |
| UI/UX Pro Max | persona/org | B− | CLI global |
| logo-designer-skill (neonwatty) | persona | B− | Pequeño |
| IFixAi | empresa | C | Datos a un juez; pruebas reales |
| Impeccable | persona | C | Hooks fuera de aprobación; binario; bypass |
| ponytail | persona | C | Hooks cada sesión |
| i-have-adhd | persona | C | Hooks y scripts sin ver |
| Paperclip | empresa | C | Telemetría activa; modo sin autenticación |
| Hermes Agent | Nous Research | C | `curl \| bash`; backend local |
| /brag | org | C | npx sin versión fija |
| VoiceStudio | persona | C | Muchos instaladores; clonación de voz |
| ArtCraft / PhotoCraft | cuenta `storytold` | C | Alfa; nube; sin política de privacidad |
| OpenMontage | owner ambiguo | C | Dueño no coincide; gasta dinero |
| Strix | org | C | Ofensivo (solo autorizado) |
| claude-squad | org | C | `curl \| bash`; autoyes |
| bernstein | 1 mantenedor | C | Beta |
| taste-skill | persona | C | extract.js; escribe en CLAUDE.md |
| AI Job Search | org/persona | C | CLI pre-aprobados; tus datos personales |
| concurrent-playwright-mcp | persona | C | HTTP sin autenticación |
| Last 30 Days | persona | D | Cookies; auto-actualización |
| Agent-Reach | persona | D | Copia sesiones de navegador |
| REA | persona | D | Modifica agentes; ejecuta muestras |
| OpenClaw | fundación | D (por defecto) | Sin sandbox por defecto |
| playwright-parallel-mcp | persona | D | npm arbitrario `@latest` |

---

## 13. Lo que no pude verificar o resolver

- **Reddit:** inaccesible para mi buscador. No hay hilos de Reddit en esta investigación.
- **Código real:** no audité scripts, hooks ni binarios de ningún repo. Las notas son de nivel README.
- **Fechas del último commit:** las páginas de GitHub que leí no las mostraban. No puedo decir qué repos están activamente mantenidos.
- **Estrellas:** varias cifras son anómalas (REA 457.7k con 112k forks; ponytail 190k; OpenMontage 80k). No pude revisar su historial de estrellas.
- **CrewAI, nexu-io/open-design, whawkinsiv/solo-founder-superpowers, anthropics/claude-code-action:** no abrí sus páginas.
- **"iqjiy"**, el flujo "Google Omni" y la skill de "agshinrajabov": no encontré nada.
- **Número de apps de ArtCraft:** tu captura dice trece; las fuentes, siete.
- **La política de seguridad de Supabase, Google/skills y otros:** la página muestra el enlace, no su contenido.
- **Cursos de Anthropic Academy:** no pude abrir el sitio; los nombres y el texto de los certificados vienen de búsquedas y blogs (sección 16).
- **Reddit y docs.anthropic.com:** no accesibles; la documentación de claves de API de Anthropic no se pudo leer (sección 17).
- **Los otros 19 proyectos de la guía de "27 proyectos"** del post de Instagram: no los conozco (sección 19).
- **El libro de Snowflake:** solo vi las páginas 28 y 30 a 35.

---

## 14. Conceptos de IA, de lo básico a lo avanzado (con el libro de Snowflake)

### 14.1 De dónde sale esto

- **Fuente:** tus capturas del libro *Generative AI and LLMs For Dummies, Snowflake Special Edition* (John Wiley & Sons, © 2024): la página 28 (final del Capítulo 3, con la Figura 3-2) y las páginas 30 a 35 del **Capítulo 4, "Bringing LLM Apps into Production"**. La página 28 trae el índice del capítulo 4: caché semántica, feature injection y context retrieval; inferencia; interfaces amigables; orquestación de agentes.
- **Sesgo a tener en cuenta:** es una edición especial **patrocinada por Snowflake**, una empresa de plataformas de datos. Sus recomendaciones ("una plataforma de datos completa", "mantener el procesamiento cerca de los datos") favorecen su tipo de producto. Úsalo como material de aprendizaje, no como consejo neutral.
- **Dato desactualizado:** el libro dice que la ventana de contexto "típicamente solo contiene unos pocos miles de tokens". Eso era cierto en parte en 2023-2024; hoy los modelos aceptan mucho más texto. Verifica la cifra vigente en la documentación del modelo que uses. (Esto último es mi conocimiento general, no algo que leí en una fuente hoy.)
- **Los proyectos "valorados"** no vienen del libro: son de un post de Instagram que enviaste después (sección 19). El "glaciar" de conceptos no es del libro, es el iceberg de términos de Claude Code (sección 20).

### 14.2 Los conceptos del libro, explicados

La columna "Lo que dice el libro" resume las capturas. Las columnas "En simple" y "Cuidado" son mi explicación.

| Concepto | Lo que dice el libro | En simple | Cuidado / conexión con tu seguridad |
|---|---|---|---|
| **Cloud data platform** (Cap. 3, Fig. 3-2) | Una plataforma de datos en la nube reúne el trabajo de analistas, científicos e ingenieros de datos. Los proveedores ofrecen herramientas para construir apps de IA generativa (chatbots, buscadores) y para cargar y consultar documentos (contratos, facturas, arriendos) usando el motor de razonamiento del LLM. Si incluye un **marketplace**, puedes usar apps de otros usuarios y empaquetar y distribuir tus propios modelos. La Figura 3-2 muestra capas: *Cross-Cloud & Global* → *Cloud Services* (rendimiento, gestión, alta disponibilidad, seguridad y gobierno, metadatos, compartir y colaborar) → *Elastic Multicluster Compute* (Python, SQL, Java, Scala) → *Optimized Storage* (datos no estructurados, semiestructurados y estructurados). | Un lugar único donde viven tus datos y se ejecutan los modelos, con seguridad y permisos integrados. | Es texto comercial de Snowflake. **Un marketplace de apps de terceros tiene el mismo problema de confianza que un marketplace de plugins** (sección 3 y 20): lo publica un tercero y corre con tus datos. |
| **Semantic caching** (caché semántica) | Guardar temporalmente representaciones semánticas (embeddings) de los datos. Un chatbot puede precalcular las de muchas consultas posibles y recuperarlas de la caché en vez de procesar desde cero; reduce el cómputo y acelera la respuesta. | Recordar el resultado de preguntas con el mismo significado aunque estén escritas distinto, para no volver a pagar el cálculo. | Si la caché no separa usuarios o permisos, podría devolver a una persona algo que era de otra. No guardes datos sensibles en caché sin controles. |
| **Feature injection** | Incorporar información o características adicionales al modelo para mejorar su rendimiento y razonamiento ("inyectar features relevantes" a un prompt). | Darle al modelo, en el momento de la consulta, los datos extra que importan para esa tarea. | Es un término poco estándar y su definición es amplia. **Todo lo que inyectas es una puerta de entrada para prompt injection** si viene de fuentes no confiables. |
| **Context retrieval** (recuperación de contexto) | Traer el contexto relevante (interacciones previas, historial del usuario) para respuestas más precisas y personalizadas. Ejemplo: un soporte que recupera el número de pedido que el cliente mencionó antes. | La "memoria de corto plazo" que se le entrega al modelo. | El historial puede contener datos personales; decide qué se guarda y por cuánto tiempo. |
| **RAG** (retrieval-augmented generation) | Conectar el LLM a fuentes externas para darle información actual (noticias, investigación). Los textos largos no caben en la ventana de contexto, así que se dividen en trozos (chunks). Paquetes como LangChain lo automatizan. | Antes de responder, el sistema busca los fragmentos relevantes en tus documentos y se los pasa al modelo. | Los documentos buscados son contenido no confiable. Un PDF o una página con texto malicioso puede intentar darle órdenes al modelo. |
| **ICL** (in-context learning) | El libro menciona que RAG es un tipo de aprendizaje en contexto. | El modelo "aprende" de ejemplos o información dentro del prompt, sin reentrenarse. | Lo aprendido dura solo mientras esa información esté en el contexto. |
| **Ventana de contexto y chunks** | Cada trozo debe caber en la ventana de contexto; los trozos se encadenan. | La ventana es cuánto texto puede "tener a la vista" el modelo a la vez. Un token es un fragmento de palabra. | Más texto en contexto = más costo y más superficie para inyección. |
| **Processing for inference** (inferencia) | Ejecutar los cálculos para aplicar un modelo ya entrenado a datos nuevos y generar predicciones. Tres consideraciones: **Infraestructura** (las GPU manejan cómputo en paralelo; la elección depende del tamaño del modelo, memoria y latencia), **Acceso** (modelo en servidores propios o en la nube; la nube da escalabilidad pero plantea seguridad y gobierno de datos) y **Consumo** (por APIs y funciones, o por interfaces propias de la aplicación). | Inferencia = usar el modelo; entrenar es construirlo. | **Acceso en la nube significa que tus datos viajan a un tercero.** Revisa qué retiene y si entrena con ellos antes de enviar información sensible. |
| **Reducing latency** (reducir la latencia) | Latencia = tiempo que tarda el LLM en predecir tras recibir la entrada. Es crítica en chatbots de soporte. El libro recomienda mantener el procesamiento cerca de los datos. | Cuánto esperas la respuesta. | Acelerar con cachés o atajos puede sacrificar frescura o seguridad. |
| **Tipos de interfaz** | Chat (conversar), apps de escritorio (mucho cómputo o recursos locales) y CLI (para desarrolladores, por ejemplo para generar código). | Claude Code es una CLI; Claude.ai es chat. | Las CLI y apps de escritorio con acceso local pueden tocar tus archivos: ahí importan los permisos. |
| **Simplifying development and deployment** | Una plataforma completa debería ofrecer cuatro capacidades: alto rendimiento, escalabilidad, **seguridad integral** (procesar datos sensibles según políticas empresariales) y facilidad de uso. | Lista de verificación para elegir dónde construir. | Sesgo: lo escribe un proveedor de plataforma. |
| **LLMOps** | Herramientas nuevas que emergen bajo ese paraguas ("operaciones de LLM"). | Lo que MLOps es al aprendizaje automático, pero para modelos de lenguaje. | — |
| **Orchestration** (orquestación) | Coordinar agentes y otros componentes para lograr objetivos. El libro define los agentes de IA como "instancias individuales de modelos de lenguaje responsables de tareas específicas" (resumir, traducir, analizar sentimiento) y la orquestación como organizar agentes, coordinar entradas y salidas y gestionar el flujo de datos. Ejemplo e-commerce: consultar el pedido en una base de datos, generar una etiqueta de devolución con la API de un transportista, confirmar datos del cliente e iniciar la devolución. | El director de orquesta que reparte tareas y junta resultados. | La definición de "agente" del libro es más simple que la de hoy (un agente actual usa herramientas y se repite en un bucle hasta terminar). |
| **Orchestration library** (Figura 4-1) | El diagrama muestra: Aplicación de usuario ⇄ **Librería de orquestación** ⇄ LLM, y la librería conecta además con fuentes de datos externas (documentos, base de datos, web) y aplicaciones externas. Sirve para que los datos fluyan sin fricción. | La capa intermedia que conecta la app, el modelo y todo lo demás. | **Cada flecha del diagrama es una entrada posible para datos no confiables y una salida posible para tus datos.** Es el mismo mapa de riesgos de la sección 17. |
| **Para qué sirven las librerías de orquestación** | Tres usos: (1) **encadenar prompts** para crear aplicaciones más complejas, (2) **conectar a fuentes de datos** (bases de datos, APIs, archivos), (3) **escalar a varios LLM**, que puede ser más difícil de mantener pero permite usar modelos especializados por tarea. | Es lo que hacen LangChain, LangGraph y similares. | Más modelos y más conexiones = más piezas que mantener y asegurar. Esto respalda mi recomendación de la sección 5: empezar simple. |

**Conexión con el resto del dossier:** la Figura 4-1 del libro es el tipo 2 de orquestador de la sección 5 (frameworks que programas tú). Los gestores de sesiones de Claude Code (tipo 1) son otra cosa: coordinan *agentes completos de código*, no solo llamadas a un modelo.

### 14.3 Escalera de conceptos (mi organización, de menos a más complejo)

Esto es mi propia ordenación didáctica con conocimiento general; no es una lista del libro.

1. **Fundamentos:** inteligencia artificial, aprendizaje automático, modelo de lenguaje (LLM), token, prompt, alucinación (respuestas inventadas con seguridad).
2. **Usar un modelo:** inferencia, latencia, API, costo por token, ventana de contexto, temperatura.
3. **Darle información:** contexto, embeddings (números que representan significado), base de datos vectorial, chunking, RAG, caché semántica.
4. **Darle manos:** uso de herramientas (tool use / function calling), conectores y MCP, permisos.
5. **Agentes:** bucle del agente (pensar, usar herramienta, observar, repetir), subagentes, skills, hooks.
6. **Orquestación:** varios agentes o sesiones, aislamiento (worktrees, contenedores), presupuestos y aprobaciones, carriles de navegador.
7. **Operar y proteger:** LLMOps, evaluaciones (evals), guardrails, observabilidad, prompt injection, gestión de secretos, sandbox.

---

## 15. Conectores y MCP en Claude Code

**Fuente:** documentación oficial de Claude Code (code.claude.com/docs/en/mcp) leída el 2026-11-14, más el artículo de ayuda de Anthropic sobre conectores (por búsqueda). Donde diga "según fuentes secundarias" no lo verifiqué en la documentación oficial.

### 15.1 Qué es cada cosa

- **MCP (Model Context Protocol):** el estándar con el que Claude Code se conecta a herramientas, bases de datos y APIs externas. Cuando conectas un servidor, Claude puede leer y actuar en ese sistema directamente, en vez de que tú copies datos al chat.
- **Servidor MCP:** el programa que ofrece las herramientas.
- **Conector:** el nombre de producto de Anthropic para una integración empaquetada que ves en un directorio (Gmail, Google Drive, Google Calendar, GitHub, Slack, Microsoft 365, etc., según fuentes secundarias). Un conector es la "cara" y un servidor MCP es lo que hay detrás.
- **Directorio de Anthropic (claude.ai/directory):** conectores revisados por Anthropic frente a sus criterios de listado. La propia documentación aclara que **Anthropic no hace auditoría de seguridad ni administra ningún servidor MCP.**
- **Conectores personalizados (custom connectors):** apuntas a cualquier servidor MCP por su URL. Anthropic no los verifica (según fuentes secundarias).

### 15.2 Tipos de conexión (transports)

| Tipo | Estado | Úsalo para |
|---|---|---|
| `http` | Recomendado | Servicios remotos en la nube; soporta OAuth |
| `sse` | **Obsoleto** | Solo servicios que únicamente ofrecen SSE |
| `stdio` | Soportado | Procesos locales y scripts propios; **ejecuta un programa en tu computador** |
| `ws` | Soportado | Servidores remotos que envían eventos; solo por JSON |

### 15.3 Comandos básicos (de la documentación oficial)

```bash
# Servidor remoto por HTTP (recomendado)
claude mcp add --transport http notion https://mcp.notion.com/mcp

# Servidor local (stdio): lo que va después de -- se ejecuta tal cual en tu equipo
claude mcp add --transport stdio airtable -- npx -y airtable-mcp-server

claude mcp list              # estado de cada servidor
claude mcp get notion        # detalle de uno
claude mcp login sentry      # flujo OAuth desde la terminal
claude mcp logout sentry     # borra credenciales
claude mcp remove notion     # elimina (OJO: no revoca el token)
```

Dentro de una sesión, `/mcp` muestra el estado y gestiona autenticación y activación.

### 15.4 Alcances (scopes): dónde queda guardado

| Alcance | Se carga en | Se comparte con el equipo | Se guarda en |
|---|---|---|---|
| **Local** (por defecto) | Solo el proyecto actual | No | `~/.claude.json` |
| **Project** | Solo el proyecto actual | Sí, por control de versiones | `.mcp.json` en la raíz |
| **User** | Todos tus proyectos | No | `~/.claude.json` |

Orden de precedencia cuando un servidor está definido en varios lugares: local, proyecto, usuario, plugins y, al final, los conectores de claude.ai.

### 15.5 `.mcp.json` y claves

El archivo `.mcp.json` se versiona (se commitea). La documentación muestra cómo usar variables en vez del valor real:

```json
{
  "mcpServers": {
    "shared-server": {
      "type": "http",
      "url": "https://api.example.com/mcp",
      "headers": { "Authorization": "Bearer ${API_KEY}" }
    }
  }
}
```

`${VAR}` se expande desde variables de entorno. **Nunca escribas la clave real dentro de `.mcp.json`**, porque el archivo se sube al repositorio. Para servidores `stdio`, el ejemplo oficial pasa una clave con `--env NOMBRE=VALOR`; eso deja el valor en tu historial de terminal y en tu configuración local, así que trátalo como un secreto.

### 15.6 Seguridad de los conectores (todo de la documentación oficial salvo lo indicado)

- **"Verifica que confías en cada servidor antes de conectarlo. Los servidores que traen contenido externo pueden exponerte a prompt injection."**
- Claude Code **pide aprobación** antes de usar servidores de un `.mcp.json` en sesiones interactivas. **Las ejecuciones sin interfaz (`-p`) no muestran ese aviso y cargan los servidores del proyecto sin preguntar.**
- Un repositorio clonado **no puede aprobarse a sí mismo** con configuraciones commiteadas en una carpeta no confiable: el servidor queda "pendiente de aprobación".
- Los **plugins y los conectores de claude.ai pueden añadir servidores fuera del repositorio**, así que revisar `.mcp.json` no muestra todos los que una sesión puede cargar.
- Las credenciales OAuth solo se envían a endpoints HTTPS o localhost.
- `claude mcp remove` **borra el token guardado pero no lo revoca** en el servidor de autorización. Si importa que quede inválido, ejecuta primero `claude mcp logout <nombre>`.
- Anthropic recomienda **escribir tus propios servidores MCP o usar los de proveedores en los que confíes**, y puedes configurar permisos por servidor.
- **Un conector con permisos de escritura puede actuar en tu nombre** (enviar correos, borrar archivos). Según fuentes secundarias se pueden restringir acciones (por ejemplo, permitir buscar y resumir correo pero no enviarlo). Da el mínimo de permisos.
- **Planes:** qué conectores están disponibles según plan varía entre fuentes secundarias (por ejemplo, Microsoft 365 solo en Team y Enterprise). Confírmalo en el artículo de ayuda de Anthropic.

### 15.7 Conectores recomendados para empezar (criterio)

Empieza con **uno** y de solo lectura: tu documentación o tus notas. Evita en las primeras semanas los que pueden enviar mensajes, pagar o borrar. Cada conector que añades es una puerta de entrada de datos no confiables y una salida posible de tus datos.

---

## 16. Cursos gratuitos de Anthropic y certificados

**Fuente y límite:** no pude abrir `anthropic.skilljar.com` desde esta sesión (el dominio está fuera de la red permitida). Los nombres de los cursos vienen del resultado de búsqueda de esa página y de blogs; el número de cursos y el texto de los certificados **varían entre fuentes**. Verifícalo tú en el sitio oficial. Si quieres que lo lea yo, hay que permitir ese dominio en la configuración de red de la sesión.

**Qué se sabe (fuentes secundarias, coinciden en lo esencial):**
- **Anthropic Academy** corre en Skilljar (`anthropic.skilljar.com`; también se llega desde `anthropic.com/learn`). Es **gratuito** y para registrarte basta un correo.
- **Cantidad:** 13, 14, 15, 17, 18, 20 o 22 cursos según el artículo y la fecha.
- **Certificado:** al completar un curso se obtiene un certificado que se puede compartir en LinkedIn ("Add to LinkedIn" de Skilljar, según una fuente). **Una reseña advierte que el certificado "certifica asistencia"**: confirma que completaste el curso, no que dominas la materia. Algunos cursos podrían mostrar solo una insignia; revisa la ficha de cada uno.

**Cursos que aparecen en el catálogo oficial según la búsqueda:**

| Curso | Para qué sirve | Nivel (según fuentes) |
|---|---|---|
| **Claude 101** | Uso básico de Claude | Principiante |
| **AI Fluency: Framework & Foundations** | Pensar y trabajar con IA de forma responsable | Principiante |
| **Claude Code 101** | Primeros pasos con Claude Code | Principiante |
| **Claude Code in Action** | Integrar Claude Code a un flujo de trabajo: leer archivos, ejecutar comandos, modificar código, contexto, GitHub (≈1 hora) | Intermedio |
| **Skills in Claude Code** | Skills (según una fuente secundaria; verifica que exista con ese nombre) | — |
| **Introduction to Model Context Protocol** | Construir servidores y clientes MCP con Python | Intermedio a avanzado |
| **Model Context Protocol: Advanced Topics** | MCP avanzado | Avanzado |
| **Building with the Claude API** | Usar la API de Claude | Desarrollador |

**Orden sugerido (mi recomendación, de básico a avanzado):** Claude 101 → AI Fluency → Claude Code 101 → Claude Code in Action → (Skills) → Intro a MCP → API → MCP avanzado. Hazlos *antes* de instalar repos de terceros: te dan el vocabulario para entender qué ejecuta cada cosa.

**Sobre LinkedIn:** publica el certificado con su nombre exacto ("certificado de finalización de curso"), sin presentarlo como una certificación profesional. Es honesto y sigue sumando.

---

## 17. Seguridad al ir rápido: qué cuidar antes de pasar a repos y agentes

### 17.1 La regla de oro sobre tus claves de API

**No le pegues tu clave de API (ni contraseñas ni tokens) a Claude, ni a ningún chat, ni a un archivo que el agente pueda leer.** Razones:
- Lo que escribes en una conversación se envía a servidores externos y queda en el historial de la sesión. Una clave así deja de ser privada.
- Un agente puede incluir esa clave en un archivo, un commit o un comando sin que lo notes.
- Una skill o una página maliciosa puede intentar convencer al agente de que la envíe a otro lado (prompt injection).

**Cómo manejarlas bien:**
1. **Variables de entorno** o un **gestor de secretos**, no texto en archivos del proyecto. `.env` fuera del control de versiones (`.gitignore`).
2. **Una clave por integración**, con el mínimo de permisos (si se filtra una de solo lectura, el daño es mucho menor).
3. **Límites de gasto y alertas** en el panel del proveedor (no pude verificar la documentación de Anthropic porque `docs.anthropic.com` no es accesible desde esta sesión; revísalo en tu consola).
4. **Rota las claves** de forma periódica y cada vez que sospeches algo.
5. **Bloquea que el agente lea tus secretos** en Claude Code. La documentación oficial indica reglas de denegación de lectura. Ejemplo en `.claude/settings.json`:

```json
{
  "permissions": {
    "deny": ["Read(./.env)", "Read(./secrets/**)"]
  }
}
```

   **Límites que la propia documentación reconoce:** las reglas `Read` se aplican **con el mejor esfuerzo** a las herramientas de lectura integradas, y **un comando Bash que apruebes puede seguir leyendo cualquier archivo que tu usuario pueda leer**. Para restringir Bash a nivel de sistema operativo, usa el **sandbox** (`/sandbox`). Un archivo `.claudeignore` **no tiene efecto**: pasa sus entradas a reglas `Read` de denegación. La documentación tiene una página con un ejemplo listo ("Exclude sensitive files").
6. **Cómo guarda Claude Code sus propias credenciales (documentación oficial):** en el llavero de macOS si está disponible; en Linux en un archivo con permisos `0600`; en Windows en un archivo que hereda los permisos de tu perfil.

### 17.2 Si se filtra una clave

Según guías de seguridad (fuentes secundarias, coinciden entre sí):
1. **Revócala o rótala primero**, antes de limpiar nada.
2. Genera una nueva, invalida la filtrada y actualiza los sistemas que la usan.
3. Borrar el archivo o reescribir el historial de git **no basta**: la clave sigue alcanzable. Un estudio de Truffle Security halló que en más del 50% de los casos las claves seguían activas después de retirarlas de GitHub (cifra de un blog, no verificada por mí).
4. Si rotas pero vuelves a escribir la clave nueva en el mismo lugar, el problema se repite.
5. Activa el **escaneo de secretos y la protección de push** de GitHub.

### 17.3 Cuidados al apurarte con repos y agentes

1. **Un cambio a la vez.** Añade una herramienta, úsala una semana, y recién entonces la siguiente. Si instalas diez cosas el mismo día, no sabrás cuál causó un problema.
2. **Entorno aislado para lo desconocido.** Una máquina virtual o contenedor sin tus cuentas ni claves. Lo recomienda la documentación de Claude Code ("usa máquinas virtuales para ejecutar scripts y llamadas a herramientas, sobre todo con servicios web externos").
3. **Aprende los modos de permiso.** La documentación indica que el modo automático (un clasificador revisa las acciones en lugar de ti) es el modo inicial de las sesiones interactivas en terminal y VS Code. Mientras aprendes, conviene usar el modo manual y ver cada acción; revisa `/permissions` y la página de modos de permiso.
4. **Tú eres responsable de revisar** lo que apruebas. Lo dice la documentación: "eres responsable de revisar el código y los comandos propuestos antes de aprobarlos".
5. **Nunca uses banderas de "aceptar todo"** (`--autoyes`, `bypassPermissions`, "dangerously-skip-permissions") fuera de un entorno desechable.
6. **No ejecutes instaladores `curl | bash`** ni `npx paquete@latest` de proyectos que no conoces. Usa el procedimiento "extraer, no instalar" (sección 2.1).
7. **Fija versiones** y anota el commit del que copiaste algo.
8. **Haz commit de tu trabajo antes de dejar que un agente edite**, para poder volver atrás.
9. **Pon topes de gasto y de pasos** en cualquier agente autónomo.
10. **Trata todo contenido externo como no confiable:** páginas web, PDF, ofertas de empleo, README, resultados de herramientas. Si un texto parece darte órdenes, es una señal de alarma.
11. **No uses cuentas reales en pruebas** (correo, redes, banco). Cuentas secundarias o de prueba.
12. **Desconfía de las estrellas y de los influencers.** Las estrellas se pueden inflar, y las listas "10 repos imprescindibles" de redes sociales no son una revisión de seguridad. Verifica el dueño real del repo (varios de tus enlaces tenían errores de nombre).
13. **Clones y copias falsas:** hay repos con nombres casi iguales a los oficiales (typosquatting). Entra siempre desde el sitio del proyecto o del autor.
14. **Cierra la sesión de lo que ya no uses:** revoca tokens OAuth de conectores que dejaste conectados.

### 17.4 Un mapa de amenazas sencillo

| Amenaza | Qué es | Defensa principal |
|---|---|---|
| **Prompt injection** | Texto en un contenido externo que intenta dar órdenes al agente | Tratar todo contenido externo como dato, aprobar acciones, aislar |
| **Skill o plugin malicioso** | Instrucciones o scripts dañinos disfrazados de herramienta útil | Leer completo, extraer solo texto inerte, entorno aislado |
| **Cadena de suministro** | Dependencias o descargas en la instalación (`npx`, `curl \| bash`, `postinstall`) | Fijar versiones, no ejecutar instaladores sin leer |
| **Filtración de secretos** | Claves en chats, commits, `.env` o logs | Variables de entorno, reglas de denegación, rotación, escaneo |
| **Permisos excesivos** | Conector o agente con más acceso del necesario | Mínimo privilegio, modo manual al aprender, sandbox |
| **Robo de sesión** | Herramientas que copian cookies o perfiles de navegador | Cuentas secundarias, no entregar tu perfil real |
| **Costos descontrolados** | Un agente que llama APIs de pago en bucle | Topes de gasto y de pasos |
| **Datos a terceros** | Tu información viaja a proveedores de modelos o servicios | Leer políticas, minimizar lo que envías |

**Recordatorio de la documentación oficial:** "aunque estas protecciones reducen significativamente el riesgo, ningún sistema es completamente inmune a todos los ataques."

---

## 18. Ruta de aprendizaje sugerida (mi propuesta)

Es mi propuesta de orden; ajústala a tu tiempo.

| Etapa | Qué hacer | Qué debes entender al terminar |
|---|---|---|
| **1. Fundamentos** | Cursos Claude 101 y AI Fluency; leer los conceptos de la sección 14.2 | Qué es un modelo, token, prompt, contexto, alucinación |
| **2. Claude Code** | Claude Code 101 y Claude Code in Action, en modo manual de permisos | Qué hace cada permiso y por qué importa aprobar con criterio |
| **3. Seguridad base** | Aplicar la sección 17.1 (reglas `Read` para `.env`, claves en variables de entorno) | Dónde viven tus secretos y quién puede leerlos |
| **4. Skills** | Extraer 2 o 3 skills oficiales de `anthropics/skills` con el método de la sección 2.1 | La diferencia entre texto inerte y código que se ejecuta |
| **5. Conectores** | Conectar **un** servidor MCP de solo lectura; curso de MCP | Qué ve y qué puede hacer cada conector |
| **6. Un solo carril** | Un Playwright headless aislado (sección 6) | Aislamiento y límites de seguridad |
| **7. Gestor de sesiones** | Probar uno oficial o de AWS Labs con una tarea pequeña y tope de gasto | Worktrees, aprobaciones, presupuesto |
| **8. Decidir orquestador** | Con tus flujos reales, decidir según la sección 5 | Qué construir y qué reutilizar |

**Proyectos de práctica (sección 19):** a partir de la etapa 5, elige uno por mes (RAG híbrido, caché semántica o Text-to-SQL de solo lectura primero). **Iceberg (sección 20):** avanza un nivel solo cuando puedas explicar el anterior.

**Señal de que vas demasiado rápido:** instalaste algo que no sabes explicar con tus palabras, o diste a un agente acceso a una cuenta real.


## 19. Proyectos de IA "valorados" para el portafolio (post de Instagram)

**Fuente:** 4 capturas de un reel de Instagram titulado *"Actually good AI Engineering Projects for your resume"* (cuenta que aparece como `bashi_fuirkashi`, con insignia de verificado; publicado "hace 1 día" respecto de tu captura). Es la opinión de un creador de contenido, **no una fuente verificada**. El post termina con: *"Comenta 'GOOD' y te envío por DM mi guía completa de 27 proyectos"*. **Solo se ven 8 de los 27 proyectos** en tus capturas; los otros 19 no los conozco.

**Sobre el gancho del post:** pedir que comentes una palabra para enviarte un DM es una técnica de captación de contactos. No es peligroso en sí mismo, pero desconfía de enlaces y archivos que lleguen por ese DM (phishing) y no entregues datos personales ni claves para recibir la guía.

### 19.1 Los 8 proyectos visibles

La columna "Qué construyes" es lo que dice el post. "Qué aprendes" y "Cuidado" son mi explicación.

| # | Proyecto | Qué construyes (según el post) | Stack (según el post) | Qué aprendes (mi explicación) | Cuidado de seguridad |
|---|---|---|---|---|---|
| 1 | **RAG Pipeline with Hybrid Search** | Combinar búsqueda por palabras clave y búsqueda semántica para responder preguntas sobre documentos de una empresa, con citas de la fuente. | Python, ChromaDB, BM25, FastAPI | RAG, embeddings, chunking y por qué mezclar dos tipos de búsqueda (sección 14.2). | Los documentos son contenido no confiable (inyección de instrucciones). Si hay documentos con distintos permisos, cada usuario debe ver solo lo suyo. |
| 2 | **Agent Orchestration System** | Agentes que dividen tareas, usan herramientas, recuerdan contexto y **piden aprobación humana**. | Python, LangGraph, PostgreSQL, Redis | Orquestación (sección 5): es exactamente el tipo 2, un framework que programas tú. Es tu mejor proyecto de práctica para la Fase 2. | Aprobación humana para acciones irreversibles, topes de gasto y de pasos, registro de acciones (reglas mínimas de la sección 5.3). |
| 3 | **Model Regression Detection System** | Probar automáticamente cambios de prompts y modelos para detectar respuestas peores antes de que lleguen a los usuarios. | Python, DeepEval, GitHub Actions, Docker | Evaluaciones (evals) y pruebas automáticas. | Las claves de API van en **GitHub Secrets**, nunca en el repo (la documentación de Claude Code lo advierte). |
| 4 | **LLM Cost Autopilot** | Enviar peticiones a modelos más baratos cuando pueden resolver la tarea y luego revisar la calidad de la respuesta. | Python, FastAPI, Scikit-learn, Streamlit | Control de costos. La documentación de subagentes de Claude Code plantea la misma idea: tareas rutinarias en un modelo más barato como Haiku. | Un enrutador mal calibrado puede mandar datos sensibles a un proveedor equivocado. |
| 5 | **Semantic Caching Layer** | Reutilizar respuestas de preguntas parecidas para reducir llamadas repetidas al LLM, tiempos de respuesta y costos. | Python, Redis, RedisVL, FastAPI | Es la **caché semántica** del libro (sección 14.2), en código. | Separar la caché por usuario o permisos; no cachear datos personales sin control. |
| 6 | **Text-to-SQL with Guardrails** | Convertir preguntas en español o inglés a consultas de base de datos, con validación y **acceso de solo lectura**. | Python, PostgreSQL, SQLAlchemy, FastAPI | Seguridad aplicada: el modelo genera SQL, pero un usuario de base de datos de solo lectura limita el daño. | Es el proyecto más relevante para ciberseguridad: una inyección de instrucciones puede terminar en una consulta peligrosa. El acceso de solo lectura y la validación son la defensa. |
| 7 | **Fine-Tuning Pipeline with LoRA** | Personalizar un modelo para una tarea, registrar experimentos y compararlo con el original. | Hugging Face PEFT, TRL, Unsloth, Weights & Biases | Ajuste fino (fine-tuning) con LoRA, que ajusta una parte pequeña del modelo. Requiere GPU y datos de entrenamiento. | Los datos de entrenamiento no deben contener secretos ni datos personales: el modelo puede memorizarlos. |
| 8 | **Failure Forensics Tool for AI Pipelines** | Rastrear cada paso de un flujo de IA para encontrar dónde falló y convertir los fallos en casos de prueba. | Python, OpenTelemetry, SQLite, Streamlit | Observabilidad: ver qué hizo cada paso. | Los registros pueden guardar prompts y datos sensibles; decide qué se guarda y por cuánto tiempo. |

### 19.2 Glosario del stack (explicación mía, de conocimiento general)

- **FastAPI:** biblioteca de Python para crear APIs web.
- **ChromaDB / RedisVL:** bases de datos o bibliotecas para guardar y buscar embeddings.
- **BM25:** algoritmo clásico de búsqueda por palabras clave (la parte "no semántica" de una búsqueda híbrida).
- **Redis:** almacén en memoria muy rápido, usado para cachés y colas.
- **PostgreSQL / SQLite / SQLAlchemy:** bases de datos y la herramienta de Python para hablar con ellas.
- **LangGraph:** framework de LangChain para flujos de agentes con estado (sección 5).
- **DeepEval:** biblioteca para evaluar respuestas de modelos de lenguaje.
- **GitHub Actions / Docker:** automatización en GitHub y contenedores.
- **scikit-learn / Streamlit:** aprendizaje automático clásico y paneles web rápidos.
- **PEFT, TRL, Unsloth:** bibliotecas para ajuste fino eficiente; **Weights & Biases:** registro de experimentos.
- **OpenTelemetry:** estándar abierto para rastrear lo que hace un sistema.

### 19.3 Orden de práctica sugerido (mi criterio)

1. **Primero (más simples):** #1 RAG híbrido, #5 caché semántica, #6 Text-to-SQL con solo lectura.
2. **Luego:** #4 enrutador de costos, #3 detección de regresiones, #8 forense de fallos.
3. **Al final (más complejos):** #2 orquestación de agentes y #7 ajuste fino con LoRA.

**Conexión con tu pregunta principal:** el proyecto #2 es literalmente "construir tu propio orquestador". Hacerlo como proyecto de aprendizaje, con aprobación humana y topes de gasto, encaja con la Fase 2 de la sección 5, después de haber usado uno existente.

**Sobre "para el currículum":** que un proyecto "se vea bien en un CV" es la opinión del autor del post y no la pude verificar. Lo que sí demuestra algo es poder **explicar cómo lo construiste, qué falla y cómo lo aseguraste**.

---

## 20. El iceberg de Claude Code: 30 términos de lo básico a lo avanzado

**Fuente de los términos:** un reel de Instagram de la cuenta `duckstreak` ("Claude Code terms, beginner to expert: the iceberg"; "all 30"). **Fuente de las explicaciones:** documentación oficial de Claude Code (code.claude.com/docs) leída el 2026-11-14. Las funciones cambian rápido y varias exigen una versión mínima de Claude Code; verifica con `/help` y la documentación.

Marcas: ✔ = confirmado en la documentación oficial.

### Nivel 1: "Tip" (superficie)

| Término | Qué es | Cuidado |
|---|---|---|
| **CLAUDE.md** ✔ | Archivo de instrucciones persistentes que Claude carga al inicio de cada sesión. | La documentación aclara que Claude lo trata como **contexto, no como configuración obligatoria**. Para bloquear una acción de forma garantizada se usa un hook `PreToolUse`. No escribas secretos aquí: el archivo suele versionarse. |
| **/init** ✔ | Inicializa un proyecto con una guía `CLAUDE.md`. | Revisa lo que genera antes de commitearlo. |
| **Plan mode** ✔ | Claude explora y escribe un plan **sin editar el código fuente** hasta que lo apruebes. Se entra con `/plan`. | Es un buen modo para aprender: ves la intención antes de la acción. |
| **/clear** ✔ | Empieza una conversación nueva con el contexto vacío (alias `/reset`, `/new`). | La conversación anterior se recupera con `/resume`. |
| **/compact** ✔ | Libera contexto resumiendo la conversación hasta ahora. | El resumen puede perder detalles; indica en qué enfocarse. |
| **/model** | Cambia el modelo que usa la sesión (la documentación lo cubre en "Model configuration"; no leí esa página completa). | Modelos más potentes gastan más. |

### Nivel 2: "Shallows" (aguas poco profundas)

| Término | Qué es | Cuidado |
|---|---|---|
| **/rewind** ✔ | Retrocede la conversación y/o el código a un punto anterior (alias `/checkpoint`, `/undo`). | Aun así, usa git como respaldo real. |
| **/resume** ✔ | Reanuda conversaciones anteriores. | — |
| **Skills** ✔ | Carpetas con `SKILL.md` que dan instrucciones para una tarea. Pueden traer scripts. | **Las líneas `` !`comando` `` se ejecutan al cargar la skill, antes de que Claude la lea**, y `allowed-tools` pre-aprueba herramientas. Revisa ambos en cualquier skill de terceros (sección 2.1). |
| **MCP** ✔ | Estándar para conectar herramientas externas (sección 15). | Verifica que confías en cada servidor. |
| **Auto mode** ✔ | Un modelo clasificador revisa las acciones **en lugar de ti** y bloquea las que juzga inseguras. | La documentación dice que **no garantiza seguridad** y no reemplaza la revisión en operaciones sensibles. |
| **Auto memory** ✔ | Notas que Claude escribe por sí mismo según tus correcciones y preferencias; se cargan al inicio (primeras 200 líneas o 25 KB). Se gestiona con `/memory`. | Puede guardar datos que mencionaste; revisa `/memory` y no menciones secretos. |

### Nivel 3: "Deep" (profundo)

| Término | Qué es | Cuidado |
|---|---|---|
| **Hooks** ✔ | Comandos de shell definidos por ti que Claude Code ejecuta en momentos del ciclo de vida (por ejemplo, tras editar un archivo). Dan control **determinista**: pasan siempre, no dependen de que el modelo decida. | **Se ejecutan con tus permisos.** Es lo primero que hay que leer en cualquier plugin de terceros. |
| **Subagents** ✔ | Asistentes especializados con su propia ventana de contexto, instrucciones, herramientas y permisos; devuelven solo un resumen. | Consumen tokens. Por defecto hasta 20 simultáneos y hasta 3 niveles de anidación. Heredan los permisos del principal. |
| **Plugins** ✔ | Paquete de skills, agentes, hooks y servidores MCP que se instala como una unidad. | **Lo que el plugin ejecuta, lo ejecuta como tú.** Un plugin habilitado forma parte de todas tus sesiones, no solo de cuando lo usas. |
| **Rules** ✔ | Instrucciones en `.claude/rules/` limitadas a ciertos tipos de archivo. | Son contexto, no una barrera. |
| **Output styles** ✔ | Cambian rol, tono y formato de las respuestas: Default, Proactive, Concise, Explanatory, Learning, o los tuyos. | Son instrucciones que Claude sigue, no garantías. **Proactive** hace que Claude empiece a trabajar sin preguntar decisiones rutinarias: no lo uses mientras aprendes. |
| **Status line** ✔ | Barra inferior personalizable que ejecuta un script que tú configuras y recibe datos de la sesión (contexto, costo, git). | **Ejecuta un comando con acceso completo**; el sandbox no la cubre. Usa solo scripts que entiendas. |

### Nivel 4: "Dark" (oscuro)

| Término | Qué es | Cuidado |
|---|---|---|
| **Non-interactive mode** ✔ | `claude -p "..."`: ejecuta sin interfaz, para scripts y CI. | **No muestra el diálogo de confianza de carpeta ni la aprobación de servidores MCP.** Sin `--bare`, carga los hooks y servidores del proyecto incluso en una carpeta que nunca has aprobado. No lo ejecutes sobre repos desconocidos. |
| **Channels** ✔ | Un servidor MCP que **empuja eventos** (Telegram, Discord, iMessage, webhooks) a tu sesión en curso. Función en "research preview". | Cualquiera que pueda responder por el canal puede **aprobar o denegar usos de herramientas** si el canal lo permite; solo se debe incluir en la lista de permitidos a quien confíes. Evítalo hasta dominar permisos. |
| **PreToolUse hooks** ✔ | Hooks que corren **antes** de que Claude use una herramienta y pueden bloquearla. | Es la forma documentada de **garantizar** una regla (por ejemplo, bloquear leer `.env`). Un script mal escrito puede dar falsa seguridad. |
| **Sandboxing** ✔ | Frontera aplicada por el sistema operativo alrededor de los comandos Bash: limita archivos y hosts de red. Se activa con `/sandbox`. Funciona en macOS, Linux y WSL2; **está desactivado por defecto**. | **No protege** las herramientas Read/Edit/Write, WebFetch, WebSearch ni los servidores MCP, hooks, líneas de estado o plugins. Los archivos de credenciales siguen legibles salvo que los restrinjas. |
| **Worktrees** ✔ | `claude --worktree nombre`: sesión aislada en su propio directorio y rama (sección 5). | El archivo `.worktreeinclude` **copia archivos ignorados por git, como `.env`, a cada worktree nuevo**: tus secretos se multiplican por cada copia. |
| **Bare mode** ✔ | `--bare`: arranque rápido que **omite** hooks, skills, plugins, MCP, auto memory y `CLAUDE.md`. Recomendado para scripts; la documentación dice que será el predeterminado de `-p` en el futuro. | Requiere `ANTHROPIC_API_KEY` en el entorno (no usa tu inicio de sesión). Es más seguro frente a hooks ajenos, pero tú pasas el contexto a mano. |

### Nivel 5: "Abyss" (abismo)

| Término | Qué es | Cuidado |
|---|---|---|
| **Claude in GitHub Actions** ✔ | Acción oficial (`anthropics/claude-code-action`): mencionas `@claude` en un issue o PR y Claude analiza, implementa y publica cambios. | La clave va como **GitHub Secret**, nunca en el repo. La app de GitHub pide un conjunto amplio de permisos (Actions, Contents, Issues, Pull requests, Workflows, entre otros); GitHub no deja aceptar solo una parte. Revisa los cambios antes de fusionar. |
| **Agent SDK** ✔ | Biblioteca (Python y TypeScript) con las herramientas y el bucle de agente de Claude Code, para tus propias aplicaciones. | Se autentica con clave de API. Hereda las herramientas de Claude Code, así que restringe permisos (sección 5.2). |
| **Your own MCP server** | Escribir tu propio servidor MCP. | La documentación recomienda **escribir tus propios servidores o usar los de proveedores de confianza**. Curso "Introduction to MCP" (sección 16). |
| **Agent teams** ✔ | Varias sesiones de Claude Code coordinadas, con lista de tareas compartida y mensajes entre ellas. **Experimental y desactivado por defecto.** | Gasta **muchos más tokens**. Los compañeros empiezan con el modo de permisos del líder, **incluido `bypassPermissions`**. Con la función activada, Claude puede formar equipos aunque no los hayas pedido. |
| **Dynamic workflows** ✔ | Un script de JavaScript que Claude escribe y que orquesta decenas o cientos de subagentes en segundo plano; se pueden guardar y reutilizar. Se activan pidiéndolo o con la palabra `ultracode`. | Pueden gastar mucho. Hay un tope de 1,000 agentes por ejecución y 16 simultáneos por defecto. En modo bypass no pide aprobación. Lee el plan antes de aprobar. |
| **Plugin marketplace** ✔ | Catálogo de plugins. Anthropic publica el **oficial** (`claude-plugins-official`) y el **comunitario** (`claude-community`); cualquiera puede publicar uno de terceros. | **Anthropic no revisa los marketplaces de terceros.** Incluso en el oficial, la documentación dice que **la mayor parte de lo que lista viene de socios y otros autores, no de Anthropic**. En el sitio claude.com/marketplace algunos plugins llevan la marca "Anthropic verified". Lee la página "Plugin security and trust" antes de instalar. |

### 20.1 Qué te enseña el iceberg sobre seguridad

1. **Cuanto más profundo, más poder y más riesgo.** Los términos de arriba (CLAUDE.md, /clear) cambian cómo conversas; los de abajo (hooks, canales, workflows, equipos de agentes) cambian **qué puede ejecutar el agente sin que lo veas**.
2. **No bajes de nivel hasta dominar el anterior.** Es la misma regla de "un cambio a la vez" de la sección 17.3.
3. **Las barreras reales son pocas:** los permisos, el sandbox, los hooks `PreToolUse` y el aislamiento por contenedor o máquina virtual. `CLAUDE.md`, las reglas y los estilos son instrucciones, no barreras.
4. **El sandbox y los permisos no cubren todo.** Hooks, líneas de estado, servidores MCP locales y plugins corren con tu acceso completo.

---

## 21. Fuentes

**Páginas de repositorios leídas en GitHub (2026-11-14):** anthropics/skills, google/skills, obra/superpowers, microsoft/playwright-mcp, anthropics/claude-quickstarts (y su README de computer-use-demo), browser-use/browser-use, openclaw/openclaw, NousResearch/hermes-agent, latent-spaces/brag, debpalash/VoiceStudio, FujiwaraChoki/supoclip, storytold/artcraft, storytold/photocraft, DietrichGebert/ponytail, ayghri/i-have-adhd, calesthio/OpenMontage, Panniantong/Agent-Reach, usestrix/strix, PaddlePaddle/PaddleOCR, DuarteSantos8/openGym, virgiliojr94/book-to-skill, supabase/supabase, Nutlope/hallmark, blader/humanizer, senlindesign/taste-skill, pbakaus/impeccable, nextlevelbuilder/ui-ux-pro-max-skill, mvanhorn/last30days-skill, D4Vinci/Scrapling, cathrynlavery/diagram-design, onyx-dot-app/onyx, paperclipai/paperclip, abi/screenshot-to-code, neonwatty/logo-designer-skill, morluto/rea, MadsLorentzen/ai-job-search, ifixai-ai/ifixai, smtg-ai/claude-squad, GoogleCloudPlatform/scion, awslabs/cli-agent-orchestrator, anthropics/claude-agent-sdk-python, langchain-ai/langgraph, openai/openai-agents-python, sipyourdrink-ltd/bernstein, dgutierrez1/concurrent-playwright-mcp, sumyapp/playwright-parallel-mcp, andyrewlee/awesome-agent-orchestrators.

**Fuentes secundarias (búsquedas web):**
- Seguridad de skills: Socket (glosario "Agent Skills"), SafeDep ("Agent Skills Threat Model"), SSW ("verify AI skill safety"), Snyk ToxicSkills (vía resúmenes en DEV Community y aiproductivity.ai), "Trust Without Verification" (dougseven.com).
- Orquestadores: Augment Code ("9 Open-Source Agent Orchestrators for AI Coding"), comparativos de Morph, Requesty, Dupple, Context Studios.
- Google skills: blog de Google Cloud ("Level up your agents: announcing Google's official skills repository").
- Computer use: documentación de Anthropic (platform.claude.com/docs/en/agents-and-tools/computer-use), InfoQ sobre el sandbox de Claude Code, Turing Post ("computer use AI agents").
- Playwright paralelo: Glama, LobeHub y páginas de los propios proyectos.
- Record a skill: Android Authority, Android Headlines, aiweekly.co, MindStudio, Eigent.
- Miro: miro.com/marketplace/miro-mcp-for-claude y developers.miro.com.
- Wispr Flow: docs.wisprflow.ai, Zapier, App Store.
- Meta Muse: DEV Community, Fast Company, DataCamp, Digit.
- OpenArt: openart.ai/features/explainer-video-maker.
- Linear: Everhour, G2.

**Fuentes añadidas en esta actualización:**
- Libro: *Generative AI and LLMs For Dummies, Snowflake Special Edition* (Wiley, © 2024), págs. 28 y 30-35 (capturas tuyas).
- Documentación oficial de Claude Code (code.claude.com/docs), leída el 2026-11-14: MCP, security, permissions, permission-modes, memory, commands, hooks-guide, sub-agents, skills, plugins/overview, plugins/anthropic-marketplaces, output-styles, statusline, headless, channels, sandboxing, worktrees, github-actions, agent-sdk/overview, agent-teams, workflows.
- Anthropic Academy: resultados de búsqueda sobre el catálogo en anthropic.skilljar.com (no pude abrir el sitio), y blogs (Analytics Vidhya, fast.io, buspa.it, vanja.io, entre otros).
- Conectores: artículo de ayuda de Anthropic (support.claude.com, "Use connectors to extend Claude's capabilities") por resultado de búsqueda, DEV Community, Zapier, Composio.
- Claves filtradas: Truffle Security, GitGuardian, Aikido, documentación de GitHub sobre secret scanning (por resultados de búsqueda).
- Posts de Instagram (capturas tuyas): `bashi_fuirkashi` (proyectos de IA) y `duckstreak` (iceberg de Claude Code). Son contenido de redes sociales, no fuentes verificadas.
- No accesibles desde esta sesión: docs.anthropic.com, anthropic.skilljar.com, fast.io, reddit.com.
