# SunSpoter
Find the best sun spots at your city
# Documentation

PROMPT PARA DESENVOLVIMENTO DE APLICATIVO WEB "SUNSPOTTER" (FULL-STACK INDEPENDENTE)

Contexto Geral: Desenvolva um aplicativo web full-stack completo chamado "SunSpotter". O objetivo principal é permitir que usuários descubram, analisem e rastreiem locais ao ar livre (spots) com base na exposição solar e condições climáticas em tempo real e futuras. O aplicativo deve ser uma ferramenta robusta e independente, fornecendo informações precisas e uma experiência de usuário fluida para entusiastas de atividades ao ar livre.

Tecnologias Core Requeridas:

Frontend: React (com TypeScript), Tailwind CSS.
Backend: Node.js (com Express.js ou Fastify) OU Python (com FastAPI/Django REST Framework).
Banco de Dados: PostgreSQL (recomendado para dados relacionais e geoespaciais) OU MongoDB (para flexibilidade de esquema).
Outras Libraries/Frameworks: React Router DOM, shadcn/ui, Lucide React, react-leaflet, TanStack Query, Framer Motion, sonner, react-markdown, recharts, Workbox (para Service Worker), axios (para requisições HTTP).
PARTE 1: REQUISITOS DO FRONTEND (CAMADA DE APRESENTAÇÃO)

1. UX/UI (Experiência do Usuário e Interface do Usuário):

*   **1.1 Design System e Estilização:**
    *   **Tailwind CSS:** Utilizar Tailwind CSS para uma estilização rápida, responsiva e baseada em classes utilitárias.
    *   **shadcn/ui:** Integrar e personalizar componentes de interface pré-construídos e acessíveis do shadcn/ui (ex: `Button`, `Card`, `Dialog`, `Select`, `Tabs`, `Sheet`, `Slider`, `Input`, `Label`, `Textarea`), estilizados com Tailwind, garantindo consistência visual e acessibilidade.
    *   **Theming:** Implementar um sistema de theming centralizado com dark mode como padrão. As cores e tipografia devem ser gerenciadas via variáveis CSS (em `index.css`) e mapeadas em `tailwind.config.js`, usando acentos em âmbar/laranja.
    *   **Responsividade:** O design deve ser inerentemente responsivo e mobile-first, adaptando-se a diversos tamanhos de tela. Deve incluir suporte a `safe-area-inset-bottom` para barras de navegação em dispositivos iOS.
*   **1.2 Navegação e Fluxo:**
    *   **Navegação Inferior:** Desenvolver um componente de navegação fixo na parte inferior da tela (ex: similar a `layout.jsx`), contendo ícones e labels para acesso rápido às seções principais (ex: "Início", "Configurações").
    *   **Navegação Interna:** Implementar botões de "Voltar" e mecanismos claros de fechamento para modais e sheets, garantindo um fluxo de usuário intuitivo e consistente.
    *   **Feedback Visual:**
        *   **Toasts:** Exibir mensagens de sucesso, erro e informação ao usuário usando a biblioteca `sonner`.
        *   **Loading Spinners:** Mostrar indicadores visuais de carregamento (`Loader2` da Lucide React ou um componente `LoadingSpinner` customizado) durante todas as operações assíncronas (ex: busca de spots, adição de spot, fetching de clima).
        *   **Offline Indicator:** Um componente de UI para informar proativamente o usuário sobre o status da conexão à internet (online/offline).
*   **1.3 Interações Chave:**
    *   **Geolocalização:** Gerenciar a solicitação e o consentimento de localização do usuário via um modal (`LocationConsentModal`), permitindo diferentes níveis de permissão (uma vez, enquanto usa, ou recusar).
    *   **Mapas Interativos:** Utilizar `react-leaflet` para exibir mapas interativos. Marcadores customizados nos mapas devem refletir o SunScore de cada spot.
    *   **Simulação de Tempo:** Incluir um `TimeSlider` que permita ao usuário arrastar para simular diferentes horas do dia, atualizando dinamicamente a posição solar e o SunScore dos spots na interface.
2. Arquitetura e Performance do Frontend:

*   **2.1 Arquitetura:**
    *   **React e TypeScript:** Serão as tecnologias base para o desenvolvimento do frontend.
    *   **React Router DOM:** Gerenciar as rotas e a navegação entre as páginas do aplicativo (configurado no `App.jsx`).
    *   **Modularização:** O código deve ser organizado em componentes pequenos, independentes e focados em suas responsabilidades (ex: pastas `components/` e subpastas temáticas), promovendo reusabilidade, manutenibilidade e escalabilidade.
*   **2.2 Gerenciamento de Estado e Dados:**
    *   **TanStack Query (`react-query`):** Implementar para um gerenciamento eficiente de estado assíncrono, cobrindo fetching, caching, sincronização e atualização de dados provenientes do backend.
    *   **Estados Locais:** Utilizar `useState` e `useMemo` para gerenciar estados específicos de componentes e otimizar cálculos ou renderizações.
*   **2.3 Performance:**
    *   **Web Workers (`useSunWorker`):** Cálculos intensivos (ex: posição solar, geodistância) devem ser descarregados para um Web Worker, garantindo que a thread principal da UI permaneça responsiva.
    *   **Caching Frontend (`cache.js`):** Implementar uma camada de cache em memória e `localStorage` para otimizar as respostas de APIs (ex: dados de clima, resultados de descoberta de POIs), reduzindo a latência e o volume de requisições de rede.
    *   **Framer Motion:** Utilizar para animações fluidas, transições de tela e micro-interações, melhorando a percepção de performance e a experiência do usuário.
*   **2.4 Outras Ferramentas/Conceitos:**
    *   **Feature Flags (`featureFlags.js`):** Implementar um sistema de feature flags para permitir ativar/desativar funcionalidades (ex: algoritmo SunScore V2, descoberta de POIs, radar de chuva) em tempo de execução, facilitando testes A/B e rollouts graduais.
    *   **Error Boundaries (`ErrorBoundary.jsx`):** Desenvolver componentes de Error Boundary para capturar erros em partes da UI, evitar que uma falha em um componente afete toda a aplicação e fornecer feedback adequado ao usuário.
PARTE 2: REQUISITOS DO BACKEND (SERVIÇOS DE DADOS E LÓGICA DE NEGÓCIO)

3. Arquitetura e Serviços de Backend:

*   **3.1 Data Models (Tabelas/Coleções de Banco de Dados):**
    *   Definir e implementar os seguintes modelos de dados no banco de dados escolhido (PostgreSQL/MongoDB):
        *   **Spot:**
            *   `id` (PK, string/UUID)
            *   `created_date` (datetime)
            *   `updated_date` (datetime)
            *   `created_by` (ID do usuário, FK para User)
            *   `name` (string, required)
            *   `type` (enum: 'quadra', 'skatepark', 'parque', 'praça', 'praia', 'pista', 'outro', required)
            *   `latitude` (number, required)
            *   `longitude` (number, required)
            *   `address` (string, optional)
            *   `description` (string, optional)
            *   `has_shade_structures` (boolean, optional, indica se tem estruturas que fazem sombra)
            *   `best_sun_hours` (string, optional, ex: "10h-15h")
            *   `orientation` (enum: 'norte', 'sul', 'leste', 'oeste', 'aberto', optional)
            *   `image_url` (string, optional, URL para uma foto do local)
            *   `osm_id` (string, optional, ID do OpenStreetMap para spots descobertos via OSM)
        *   **AppSettings:**
            *   `id` (PK, string/UUID, ex: 'global')
            *   `feature_flags` (JSON/Object, armazena flags ativas)
            *   `poi_discovery_radius_default` (number, default: 2000 metros)
            *   `weather_refresh_interval` (number, default: 10 minutos)
            *   `cache_ttl_spots` (number, default: 86400 segundos)
            *   `cache_ttl_weather` (number, default: 600 segundos)
            *   `max_spots_per_discovery` (number, default: 50)
            *   `score_version` (enum: 'v1', 'v2', default: 'v1')
            *   `shadow_model_level` (enum: 'A', 'B', 'C', default: 'C')
        *   **SpotFeedback:**
            *   `id` (PK, string/UUID)
            *   `created_date` (datetime)
            *   `spot_id` (FK para Spot, required)
            *   `user_id` (FK para User)
            *   `accuracy` (enum: 'accurate', 'inaccurate', 'partially_accurate', required)
            *   `predicted_score` (number)
            *   `actual_condition` (enum: 'muito_sol', 'sol_parcial', 'sombra', 'nublado', 'chuva')
            *   `time_of_visit` (datetime)
            *   `score_version` (string, ex: 'v1' ou 'v2')
            *   `comments` (string, optional)
        *   **FavoriteSpot:**
            *   `id` (PK, string/UUID)
            *   `created_date` (datetime)
            *   `user_id` (FK para User, required)
            *   `spot_id` (FK para Spot, required)
            *   `user_notes` (string, optional)
            *   `notify_good_conditions` (boolean, indica se o usuário quer ser alertado sobre boas condições)
        *   **UserPreferences:**
            *   `id` (PK, string/UUID, ou FK para User)
            *   `user_id` (FK para User, required)
            *   `preferred_activities` (array de strings, ex: ['skate', 'basquete'])
            *   `preferred_hours` (string, ex: 'manhã', 'tarde')
            *   `max_distance_km` (number)
            *   `min_sun_score` (number, 0-100)
            *   `home_latitude` (number, optional)
            *   `home_longitude` (number, optional)
        *   **User:**
            *   `id` (PK, string/UUID)
            *   `email` (string, unique, required)
            *   `full_name` (string)
            *   `role` (string, ex: 'admin', 'user')
            *   `created_date` (datetime)
            *   `updated_date` (datetime)
            *   (Campos adicionais para hash de senha, tokens de reset, etc.)
*   **3.2 API Layer:**
    *   Desenvolver uma API RESTful (ou GraphQL) para expor endpoints para todas as operações CRUD (Create, Read, Update, Delete) nos modelos de dados definidos.
    *   A API deve ser acessível via requisições HTTP do frontend.
    *   Implementar paginação e filtros adequados para cada endpoint.
*   **3.3 Autenticação e Autorização:**
    *   Implementar um sistema de autenticação de usuário completo (ex: baseado em JWT - JSON Web Tokens), incluindo registro, login, reset de senha e gerenciamento de sessões.
    *   A API deve proteger rotas sensíveis, garantindo que usuários autenticados só possam acessar e modificar seus próprios dados, exceto para usuários com a role 'admin' que teriam acesso privilegiado (definir regras de autorização).
*   **3.4 Lógica de Backend Customizada:**
    *   Qualquer lógica de negócio complexa, cálculos pesados ou processamento de dados (ex: cálculo avançado de SunScore que precise de dados backend, processamento de notificações) deve ser implementada como funções/serviços no backend.
    *   Isso inclui a lógica para o algoritmo SunScore V2, que pode envolver acesso a dados geoespaciais e cálculo complexo de sombra.
PARTE 3: INTEGRAÇÕES E MELHORIAS (COM FOCO EM OPEN SOURCE E FREE USAGE)

4. Integrações Externas:

*   **4.1 Dados Meteorológicos (Open-Meteo API):**
    *   Integrar diretamente com a [Open-Meteo API](https://open-meteo.com/) para buscar dados de previsão do tempo (temperatura, umidade, vento, UV, nuvens, chuva) em tempo real. Esta API é gratuita e não requer chave.
    *   **Bug Fix:** Assegurar que os dados de `time` recebidos da API sejam corretamente parseados para objetos `Date` antes de chamar métodos como `getHours()`.
*   **4.2 Descoberta de Pontos de Interesse (OpenStreetMap/Overpass API):**
    *   Integrar diretamente com a [Overpass API](https://wiki.openstreetmap.org/wiki/Overpass_API) para buscar automaticamente locais relevantes (quadras, parques, skateparks, praças, praias, pistas) do OpenStreetMap nas proximidades da localização do usuário.
*   **4.3 Radar de Chuva (RainViewer API):**
    *   Integrar diretamente com a [RainViewer API](https://www.rainviewer.com/api.html) para exibir um overlay de radar de chuva animado no `MapView`.
*   **4.4 Analytics (Solução Customizada/Open Source):**
    *   Implementar um sistema de rastreamento de eventos usando uma solução open source como [Matomo](https://matomo.org/) (auto-hospedado ou via serviço pago) ou [PostHog](https://posthog.com/) (auto-hospedado ou via SaaS).
    *   Garantir que todos os dados de rastreamento sejam anonimizados para proteger a privacidade do usuário.
5. Melhorias e Novas Funcionalidades (Abordando Gaps e Falhas):

*   **5.1 Service Worker para PWA (Progressive Web App):**
    *   Implementar um Service Worker (utilizando [Workbox](https://developer.chrome.com/docs/workbox/) ou uma implementação manual) para:
        *   **Suporte Offline Robusto:** Permitir que o aplicativo funcione (pelo menos parcialmente) sem conexão com a internet.
        *   **Cache Estratégico de Assets:** Armazenar recursos estáticos (HTML, CSS, JS, imagens) e dados de API (clima, spots) usando estratégias como Cache-First, Stale-While-Revalidate, etc.
        *   **Notificações Push:** Preparar a infraestrutura para futuras notificações web push (requer consentimento do usuário).
*   **5.2 Acurácia do SunScore e Dados de Sombra (Nível B/A):**
    *   **Backend:** Explorar e integrar fontes de dados geoespaciais 3D para obter a geometria de edifícios (building footprints e alturas).
        *   **Fontes (Open Source/Free - com ressalvas de cobertura/licenciamento):**
            *   [OpenStreetMap (OSM)](https://wiki.openstreetmap.org/wiki/3D_buildings): Utilizar dados de `building:height` e `building:levels` do OSM. Pode ser necessário processamento adicional com bibliotecas como [OSM2World](https://wiki.openstreetmap.org/wiki/OSM2World) ou frameworks como [CesiumJS](https://cesium.com/docs/cesiumjs/) para análises 3D.
            *   **Dados Governamentais/Municipais Abertos:** Pesquisar por dados de edifícios 3D disponibilizados por cidades ou governos locais.
        *   **Lógica de Sombra:** O algoritmo SunScore V2 deve utilizar esses dados 3D para simular sombras dinamicamente, aumentando significativamente a precisão da avaliação de "sombra" em um spot.
*   **5.3 Otimização de Imagens e Assets Visuais:**
    *   **Automatização:** Automatizar a conversão de imagens para formatos modernos (WebP, AVIF) durante o processo de build ou no upload para o servidor.
    *   **Lazy Loading:** Implementar carregamento tardio (`lazy loading`) para todas as imagens fora da viewport inicial.
    *   **Responsividade:** Utilizar `srcset` e elementos `<picture>` para servir diferentes tamanhos e formatos de imagem com base nas características do dispositivo e da tela.
    *   **CDN (Opcional, mas recomendado):** Integrar um Content Delivery Network (CDN) para servir assets estáticos de forma mais rápida e eficiente.
*   **5.4 Geocoding / Reverse Geocoding no "Adicionar Spot":**
    *   **Geocoding:** No formulário de adição de spot (`AddSpotModal`), integrar uma API de geocoding (ex: [Nominatim do OpenStreetMap](https://nominatim.org/release-docs/latest/api/Overview/)) para preencher automaticamente Latitude e Longitude a partir de um endereço digitado pelo usuário.
    *   **Reverse Geocoding:** Ao usar a localização atual do usuário, preencher o campo de endereço com o endereço legível obtido a partir das coordenadas.
*   **5.5 Notificações Personalizadas de "Boas Condições":**
    *   **Backend (Lógica Agendada):** Criar uma tarefa agendada (ex: cron job, worker assíncrono) no backend que periodicamente (ex: a cada hora) verifique o SunScore atual e futuro de `FavoriteSpots` em relação às preferências `notify_good_conditions` e `min_sun_score` do usuário.
    *   **Serviço de Notificação:** Utilizar um serviço de e-mail transacional (ex: [SendGrid](https://sendgrid.com/), [Resend](https://resend.com/)) para enviar alertas por e-mail. Se o Service Worker estiver implementado, também considerar notificações web push.
*   **5.6 Visualização Histórica de SunScore e Clima:**
    *   **Backend (Armazenamento de Dados Históricos):** Adaptar a integração com Open-Meteo para buscar dados históricos de clima. Armazenar um resumo desses dados, juntamente com o SunScore calculado, em uma nova coleção/tabela (`SpotHistoricalData`) no banco de dados.
    *   **Frontend (Visualização):** Utilizar a biblioteca de gráficos [Recharts](https://recharts.org/en-US/) para exibir tendências históricas de SunScore, temperatura, nuvens, etc., para um spot selecionado.
*   **5.7 Sugestões Personalizadas Baseadas em `UserPreferences`:**
    *   **Frontend (Lógica de Filtro/Ordenação):** Implementar lógica no `Home.jsx` para filtrar e ordenar os resultados de spots exibidos com base nas `UserPreferences` do usuário (atividades preferidas, horários, distância máxima, min_sun_score).
    *   **UI para Preferências:** Desenvolver uma seção dedicada na página de `Settings` onde o usuário possa definir e gerenciar suas `UserPreferences`.
6. Obtenção de Dados em Tempo Real com Melhores Condições (Discussão de Alternativas e Oportunidades Futuras):

*   **Microclima:** Para precisão hiper-local, mencionar que APIs meteorológicas mais avançadas (ex: Dark Sky/Apple Weather, AccuWeather, Tomorrow.io) podem ser exploradas, **observando que geralmente são soluções pagas**.
*   **Qualidade do Ar:** Integrar uma API de qualidade do ar (ex: [OpenAQ](https://openaq.org/) - open source, mas com cobertura e dados variados) para adicionar uma dimensão de saúde à decisão de usar um spot.
*   **WebSockets:** Para atualizações em tempo real (ex: status de conexão, pequenas mudanças climáticas instantâneas em um `SpotDetail` aberto), considerar a implementação de WebSockets no backend e frontend.
7. Ecossistema Open Source e Free Usage (Aprofundamento em Alternativas):

*   **Map Tiles:** Além de CartoDB, explorar e oferecer alternativas gratuitas de tiles do OpenStreetMap (ex: tiles padrão do OSM, Stamen Toner, Stamen Watercolor) para permitir personalização visual do mapa.
*   **Visualização Geoespacial 3D:** Investigar [CesiumJS](https://cesium.com/docs/cesiumjs/) como uma biblioteca open source para visualização de globos 3D de alta performance. Isso poderia ser um caminho para integrar modelos de sombra mais complexos e uma experiência visual imersiva.
*   **Fontes de Dados Geográficos Adicionais:**
    *   [OpenTopoMap](https://opentopomap.org/): Para mapas de topografia e elevação, úteis para análises de terreno.
    *   [NASA Earthdata](https://earthdata.nasa.gov/) / [NOAA](https://www.noaa.gov/): Para dados climáticos históricos, satélites e dados ambientais brutos (requerem processamento complexo).
Instruções de Desenvolvimento Gerais:

Código Limpo: Manter o código limpo, modular e aderente às melhores práticas de desenvolvimento (SOLID, DRY, princípios de design patterns).
Testes: Implementar testes unitários, de integração e end-to-end para garantir a qualidade e robustez da aplicação.
Documentação: Documentar as APIs do backend e os componentes complexos do frontend.
Segurança: Priorizar a segurança em todas as camadas da aplicação, desde a autenticação e autorização até a proteção contra vulnerabilidades comuns (OWASP Top 10).
Escalabilidade: Projetar a arquitetura com escalabilidade em mente, usando padrões e tecnologias que possam suportar o crescimento futuro.
CI/CD: Configurar um pipeline de Integração Contínua/Entrega Contínua para automatizar testes e deployments.
Versionamento: Usar Git para controle de versão.
Estrutura de Projeto: Manter uma separação clara entre as estruturas de pastas do frontend e do backend.
Visualização da Estrutura Funcional (ASCII Art Desvinculado):

+-----------------------------------------------------------------------------------+
|                              SunSpotter Application                             |
|                    (Full-Stack Independente: React, [Node/Python], DB)          |
+-----------------------------------------------------------------------------------+
|                                                                                   |
|  +-----------------------------------------------------------------------------+  |
|  |                           Frontend (React/TypeScript)                       |  |
|  | +-------------------------------------------------------------------------+ |  |
|  | | App.jsx (Router, Contexts: Auth, QueryClient)                           | |  |
|  | +-------------------------------------------------------------------------+ |  |
|  |                                     | (Routes to pages)                       |  |
|  |                                     V                                         |  |
|  |                 +-----------------------------------------------------+     |  |
|  |                 |               Layout.jsx (Global UI)                |     |  |
|  |                 | (Nav, CSS Vars, Safe Areas, Offline Indicator)      |     |  |
|  |                 +-----------------------------------------------------+     |  |
|  |                                     |                                         |  |
|  |                                     V                                         |  |
|  |                 +-----------------------------------------------------+     |  |
|  |                 |                   pages/Home.jsx                    |<----+--------------+
|  |                 | (Main Page: Discover, View, Add Spots, Weather)     |     |              |
|  |                 | +-------------------------------------------------+ |     |              |
|  |                 | | State: userLocation, viewMode, weather,         | |     | [Data Models]  |
|  |                 | |        selectedSpot, simulatedHour              | |     | (User, Spots,   |
|  |                 | +-------------------------------------------------+ |     | Preferences,   |
|  |                 | | Use: Custom API Calls (Spots, Favorites, Users) | |     | Feedback)      |
|  |                 | +-------------------------------------------------+ |     |              |
|  |                 +----------------------------^--------------------------+     +--------------+
|  |                                              |                                |
|  |         +--------------------------------------------------------------+        |
|  |         |                        Components (UI & Logic)               |        |
|  |         +--------------------------------------------------------------+        |
|  |         |                                                              |        |
|  |         | +----------------------------------------------------------+ |        |
|  |         | | SpotCard.jsx (Displays spot info, SunScore, weather)     | |        |
|  |         | +----------------------------------------------------------+ |        |
|  |         |                                                              |        |
|  |         | +----------------------------------------------------------+ |        |
|  |         | | MapView.jsx (Leaflet Map, Markers, User Location,        |<---+     |
|  |         | |             RainViewer overlay)                          |    |     |
|  |         | +----------------------------------------------------------+ |    |     |
|  |         |                                                              |    |     |
|  |         | +----------------------------------------------------------+ |    |     |
|  |         | | WeatherCard.jsx (Displays current & hourly weather)      |<---+     |
|  |         | +----------------------------------------------------------+ |    |     |
|  |         |                                                              |    |     |
|  |         | +----------------------------------------------------------+ |    |     |
|  |         | | SunArc.jsx (Visualizes Sun Path for a location)          | |    |     |
|  |         | +----------------------------------------------------------+ |    |     |
|  |         |                                                              |    |     |
|  |         | +----------------------------------------------------------+ |    |     |
|  |         | | AddSpotModal.jsx (Form to add new spots, Geocoding)      |<---+----+-----+
|  |         | +----------------------------------------------------------+ |    |    |     |
|  |         |                                                              |    |    |     |
|  |         | +----------------------------------------------------------+ |    |    |     |
|  |         | | ExploreView.jsx (Discovers new spots via Overpass API)   |<---+-------+-----> [OpenStreetMap/Overpass API]
|  |         | +----------------------------------------------------------+ |    |    |         (for POI Discovery)
|  |         |                                                              |    |    |
|  |         | +----------------------------------------------------------+ |    |    |
|  |         | | SpotDetail.jsx (Detailed view of selected spot, Feedback)| |    |    |
|  |         | +----------------------------------------------------------+ |    |    |
|  |         |                                                              |    |    |
|  |         | +----------------------------------------------------------+ |    |    |
|  |         | | TimeSlider.jsx (Simulates time of day)                   | |    |    |
|  |         | +----------------------------------------------------------+ |    |    |
|  |         |                                                              |    |    |
|  |         | +----------------------------------------------------------+ |    |    |
|  |         | | DebugPanel.jsx (Dev tools: Flags, Cache, Analytics config)| |    |    |
|  |         | +----------------------------------------------------------+ |    |    |
|  |         |                                                              |    |    |
|  |         | +----------------------------------------------------------+ |    |    |
|  |         | | LocationConsentModal.jsx (Manages location permissions)  | |    |    |
|  |         | +----------------------------------------------------------+ |    |    |
|  |         +---------------------------^----------------------------------+    |    |
|  |                                     | (Local Services/Utilities)              |    |
|  |                            +-------------------------------------------+        |    |
|  |                            |        Services & Utilities (Frontend)    |        |    |
|  |                            +-------------------------------------------+        |    |
|  |                            |                                           |        |    |
|  |                            | - WeatherService.js (Fetches weather data)|<-----------+-----> [Open-Meteo API]
|  |                            | - SunCalculator.js (Sun position, scores) |        |         (for Weather Forecast)
|  |                            | - useSunWorker.js (Offloads heavy calcs)  |        |
|  |                            | - featureFlags.js (Feature management)    |        |
|  |                            | - cache.js (In-app caching layer)         |        |
|  |                            | - poiDiscovery.js (OSM POI discovery)     |        |
|  |                            | - RainViewer.jsx (Weather radar overlay)  |        |
|  |                            +-------------------------------------------+        |
|  +-----------------------------------------------------------------------------+    |
|                                                                                   |
|  +-----------------------------------------------------------------------------+  |
|  |                           Backend ([Node/Python])                           |  |
|  | +-------------------------------------------------------------------------+ |  |
|  | | API Layer (RESTful/GraphQL API for CRUD on Data Models)                 | |  |
|  | | Authentication (JWT-based), Authorization (Roles)                       | |  |
|  | | Custom Logic (SunScore V2 logic, Shadow Model, Notifications, History)  | |  |
|  | +-------------------------------------------------------------------------+ |  |
|  |                                     |                                         |  |
|  |                                     V                                         |  |
|  |                 +-----------------------------------------------------+     |  |
|  |                 |                     Database ([PostgreSQL/MongoDB]) |     |  |
|  |                 | (Stores User, Spot, AppSettings, SpotFeedback,      |     |  |
|  |                 |  FavoriteSpot, UserPreferences, SpotHistoricalData) |     |  |
|  |                 +-----------------------------------------------------+     |  |
|  |                                                                           |  |
|  | +-------------------------------------------------------------------------+ |  |
|  | | External Integrations (Backend-initiated):                               | |  |
|  | | - Geocoding (Nominatim/OpenStreetMap)                                   | |  |
|  | | - Email Service (SendGrid/Resend for Notifications)                     | |  |
|  | | - Analytics (Matomo/PostHog API)                                        | |  |
|  | | - Scheduled Tasks (Cron jobs for Notifications/Historical Data)         | |  |
|  | | - 3D Building Data (OSM processing, government data, CesiumJS for analysis) | |  |
|  | +-------------------------------------------------------------------------+ |  |
|  +-----------------------------------------------------------------------------+  |
+-----------------------------------------------------------------------------------+
