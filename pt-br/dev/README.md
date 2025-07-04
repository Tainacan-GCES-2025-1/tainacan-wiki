# Tainacan para Desenvolvedores

Como você sabe, **Tainacan é um plugin do [WordPress](https://br.wordpress.org)** e é construído sobre esta plataforma muito conhecida. Se você está familiarizado com o _WordPress_, será fácil entender como o Tainacan está organizado, como ele interage com o banco de dados e como construir seus próprios recursos a partir dele.

## Bem, mas eu sou novo no WordPress

Se você não tem experiência com WordPress e gostaria de desenvolver um plugin para o Tainacan, ou mesmo contribuir para o plugin, é uma boa ideia aprender alguns fundamentos. Eles serão úteis para tudo com que você trabalhará no Tainacan.

Esta é uma lista não exaustiva dos tópicos mais importantes que você deve conhecer:

<div class="two-columns-list">

- Classe [WP_Query](https://developer.wordpress.org/reference/classes/wp_query/ ":ignore") - Este é o coração do WordPress, a classe que fornece a interface para consultar posts no banco de dados. Toda interação com o banco de dados no Tainacan usa esta classe.
- [Tipos de Post Personalizados](https://wordpress.org/support/article/post-types/ ":ignore") e [taxonomias](https://codex.wordpress.org/Taxonomies ":ignore") - Todas as entidades do Tainacan, como coleções, metadados e itens, são tipos de post personalizados do WordPress, então é útil entender como eles são manipulados.
- [O Loop](https://developer.wordpress.org/themes/basics/the-loop/ ":ignore") - Um dos principais elementos do WordPress usado para interagir com posts. Útil especialmente se você estiver trabalhando com temas.
- [Tags de Template](https://developer.wordpress.org/themes/basics/template-tags/ ":ignore") - Funções simples usadas por desenvolvedores de temas para exibir conteúdo dinâmico. Normalmente, essas funções são usadas dentro do "Loop" e o Tainacan implementa [suas próprias tags de template](/dev/template-tags.md).
- [Hierarquia de Templates](https://developer.wordpress.org/themes/basics/template-hierarchy/ ":ignore") - Crucial se você estiver trabalhando com temas.

</div>

---

## Recursos de Desenvolvimento

### Conceitos Básicos {docsify-ignore}

<div class="two-columns-list">

- [Configurando ambiente local](/dev/setup-local.md) - Se você quer contribuir para o núcleo do Tainacan, deve configurar seu ambiente local. Alternativamente, você pode usar nosso [repositório Docker](https://github.com/tainacan/tainacan-docker ":ignore"). **Se você quer desenvolver temas ou plugins, não precisa disso**.
- [Conceitos-Chave](/dev/key-concepts.md) - Primeiras coisas primeiro. Vamos entender o que é o quê no Tainacan.
- [Estrutura Interna do Tainacan](/dev/internal-api.md) - Referência sobre as principais classes do Tainacan e como usá-las.
- [Hooks do Tainacan](/dev/hooks.md) - Expanda ou modifique diferentes seções de código sem modificar o plugin, usando Actions e Filters, tanto no backend quanto no frontend.
- [API do Tainacan](https://redocly.github.io/redoc/?url=https://github.com/tainacan/tainacan-wiki/raw/master/dev/openapi.json ":ignore") - Uma API REST JSON que você pode usar para obter conteúdo de um banco de dados do Tainacan.
- [Funções e Capacidades](/dev/roles-capabilities.md) - Informações básicas sobre privacidade de dados e níveis de acesso no Tainacan.

</div>

### Mais sobre Desenvolvimento de Plugins {docsify-ignore}

<div class="three-columns-list">

- [Exportando e Expondo](/dev/exporting-and-exposing.md)
- [Importador CSV](/dev/csv-importer.md)
- [Importador de Vocabulário](/dev/vocabulary-importer.md)
- [Padrões de Mapeamento](/dev/mapping-standards.md)
- [Métodos de Repositório](/dev/repository-methods.md)

</div>

### Extensão de Plugins {docsify-ignore}

<div class="two-columns-list">

- [Criando um Novo Tipo de Metadado](/dev/creating-metadata-type.md) - Um guia para criar seu Tipo de Metadado personalizado
- [Criando um Novo Tipo de Filtro](/dev/creating-filters-type.md) - Um guia para criar seu Tipo de Filtro personalizado
- Como criar [Exportadores](/dev/exporter-flow.md), [Importadores](/dev/importer-flow.md) e [Expositores](/dev/exposers.md)
- [Registrando Novos Componentes Vue](/dev/registering-custom-vue-components.md) - Registrando novos componentes Vue que podem ser usados pelo seu plugin, como tipos de metadados e filtros ou modos de visualização extras.
- [O Componente de Lista de Itens Vue](/dev/the-vue-items-list-component.md) - A lista de itens renderizada no lado do cliente que fornece todo o poder da busca facetada.
- [Ajustando a Interface de Admin do Tainacan](/dev/admin-ui-options.md) - Usando um filtro para definir variáveis para personalizar o painel de administração do Tainacan.

</div>

### Desenvolvimento ou Extensão de Temas {docsify-ignore}

<div class="two-columns-list">

- [Criando Temas Compatíveis](/dev/creating-compatible-themes.md) - Uma introdução sobre como criar um tema que suporte totalmente os recursos do Tainacan
- [Templates Personalizados do Tainacan](/dev/custom-templates.md) - Templates personalizados que o Tainacan adiciona à Hierarquia de Templates do WordPress
- [Personalizando a Lista de Itens](/dev/customizing-the-items-list.md) - Ajustando a aparência da lista de itens
- [Criando Modos de Visualização Extras](/dev/extra-view-modes) - Como criar Modos de Visualização Extras que estarão disponíveis para exibir listas de itens
- [Suporte ao Gutenberg em Temas](/dev/theme-gutenberg-support.md) - Detalhes para oferecer melhor suporte ao editor de blocos de conteúdo

</div>

### Configuração e Performance {docsify-ignore}

<div class="three-columns-list">

- [Busca Facetada](/dev/faceted-search.md)
- [Mecanismo de Busca](/dev/search-engine.md)
- [Garbage Collector](/dev/garbage-collector.md)

</div>

---

## Outras Contribuições

<div class="three-columns-list">

- [Diretrizes de Contribuição](/dev/CONTRIBUTING.md)
- [Processo de Release do Tainacan](/dev/release.md)

</div>
