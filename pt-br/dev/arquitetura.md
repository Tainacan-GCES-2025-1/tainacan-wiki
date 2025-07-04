# Arquitetura Técnica

Este documento consolida a arquitetura técnica do Tainacan, detalhando sua estrutura como um plugin WordPress.

Os tópicos abordam desde o funcionamento interno do plugin (entidades, repositórios, controladores e lógica de negócios), passando pela API REST pública e seus recursos, até a modelagem e manipulação de dados no banco relacional. Também são descritas soluções para importação/exportação de dados e processos assíncronos.

## 1. Backend do Plugin Tainacan

O Tainacan é desenvolvido como um plugin WordPress, aproveitando sua estrutura nativa e estendendo-a com entidades personalizadas para coleções digitais. O backend é dividido em camadas:

* **Entidades**: Representações de objetos de negócio (coleções, itens, metadados etc.)
* **Repositórios**: Responsáveis por abstrair o acesso ao banco de dados
* **Controladores**: Gerenciam requisições recebidas via API REST

Existem outras partes do código que lidam com funcionalidades relacionadas ao consumo e manipulação de dados, mas estas três são as mais relevantes.

### 1.1 Principais Entidades e Repositórios

A tabela a seguir mostra as principais entidades do sistema, seus repositórios correspondentes e a localização dos arquivos de implementação. Cada entidade possui um repositório dedicado que gerencia suas operações de persistência e recuperação.

| Entidade                          | Repositório                               | Localização do Código                     |
|----------------------------------|------------------------------------------|------------------------------------------|
| Coleção (`tainacan-collection`)  | `Tainacan\Repositories\Collections`      | `class-tainacan-collections.php`         |
| Item (`tainacan-item`)           | `Tainacan\Repositories\Items`            | `class-tainacan-items.php`               |
| Metadado (`tainacan-metadatum`)  | `Tainacan\Repositories\Metadata`         | `class-tainacan-metadata.php`            |
| Seção de Metadados               | `Tainacan\Repositories\Metadata_Sections`| `class-tainacan-metadata-sections.php`   |
| Filtro                           | `Tainacan\Repositories\Filters`          | `class-tainacan-filters.php`             |
| Taxonomia                        | `Tainacan\Repositories\Taxonomies`       | `class-tainacan-taxonomies.php`          |
| Termo                            | `Tainacan\Repositories\Terms`            | `class-tainacan-terms.php`               |

### 1.2 Localizações Detalhadas das Entidades

| Entidade           | Arquivo (Caminho)                                          |
|-------------------|-----------------------------------------------------------|
| Coleção           | `src/classes/entities/class-tainacan-collection.php`      |
| Item              | `src/classes/entities/class-tainacan-item.php`            |
| Metadado          | `src/classes/entities/class-tainacan-metadatum.php`       |
| Seção de Metadados| `src/classes/entities/class-tainacan-metadata-section.php`|
| Filtro            | `src/classes/entities/class-tainacan-filter.php`          |
| Taxonomia         | `src/classes/entities/class-tainacan-taxonomy.php`        |
| Termo             | `src/classes/entities/class-tainacan-term.php`            |

## 2. API REST

A API REST do Tainacan fornece um conjunto abrangente de endpoints para gerenciar as entidades do sistema. Ela é construída sobre o padrão REST do WordPress, estendendo-o com controladores personalizados para cada recurso.

### 2.1 Recursos da API

* CRUD para coleções, itens, metadados, filtros, taxonomias
* Operações em massa e edição de múltiplos itens
* Sessões de importação/exportação
* Busca facetada e recuperação de facetas

### 2.2 Documentação Detalhada

A especificação completa da API REST com todos os endpoints, parâmetros e exemplos está disponível em:

📄 [Documentação da API REST](https://redocly.github.io/redoc/?url=https://github.com/tainacan/tainacan-wiki/raw/master/dev/openapi.json#tag/items)

### 2.3 Controladores REST Específicos

A API REST do Tainacan é estruturada através de controladores PHP especializados, cada um responsável por um recurso. Eles estendem uma classe base chamada `REST_Controller` e definem seus próprios endpoints. Exemplos:

* `REST_Collections_Controller`: Gerencia coleções (`/collections`)
* `REST_Items_Controller`: Gerencia itens (`/items`)
* `REST_Exposers_Controller`: Gerencia formatos de exposição (`/exposers`)
* `REST_Background_Processes_Controller`: Monitora processos assíncronos (`/bg-processes`)
* `REST_BulkEdit_Controller`: Manipula edição em massa de itens (`/collection/{id}/bulk-edit`)

### 2.4 Autenticação

A autenticação na API REST do Tainacan segue os padrões do WordPress. Em ambientes autenticados (como o painel de administração), cookies de sessão são usados. Para aplicações externas, recomenda-se usar os [métodos de autenticação da API REST do WordPress](https://developer.wordpress.org/rest-api/using-the-rest-api/authentication/#basic-authentication-with-application-passwords).

### 2.5 Exemplo de Uso da API

Listar itens da coleção com ID 1:

```bash
curl -X GET "https://yourdomain.com/wp-json/tainacan/v2/collection/1/items"
```

## 3. Integração Frontend ⇄ Backend ⇄ Banco de Dados

O painel administrativo do Tainacan é desenvolvido principalmente como uma Aplicação de Página Única (SPA), onde toda a obtenção e manipulação de dados é realizada através da API REST. Ciclos semelhantes de obtenção de dados também ocorrem dentro dos Blocos Gutenberg, como na Busca Facetada e no Formulário de Envio de Itens.

### 3.1 Ciclo de Requisição

1. O frontend (SPA Vue.js) envia requisições HTTP via `Axios`
2. Os controladores REST recebem e validam os dados
3. Os dados são convertidos em entidades de domínio
4. Os repositórios tratam da persistência
5. A resposta JSON formatada é retornada ao frontend

## 4. Arquitetura de Dados

A arquitetura de dados do Tainacan é baseada na infraestrutura do WordPress, utilizando e estendendo seu modelo relacional. O sistema incorpora Tipos de Post Personalizados, Taxonomias Personalizadas, Meta Post Personalizados e tabelas adicionais para operações assíncronas.

### 4.1 Estrutura de Armazenamento

| Componente                   | Tabelas WordPress            | Observações Técnicas           |
|------------------------------|------------------------------|--------------------------------|
| Coleções, Itens, Metadados   | `wp_posts`                   | Identificados por `post_type`  |
| Valores de Metadados e Termos | `wp_postmeta`, `wp_termmeta` | Pares chave-valor              |
| Taxonomias e Termos          | `wp_terms`, `wp_term_taxonomy`, `wp_term_relationships` | Categorização |
| Processos em Segundo Plano   | `wp_tnc_bg_process`          | Importação, exportação, etc.   |

### 4.2 Relacionamentos de Domínio

* Uma coleção contém múltiplos metadados e seções
* Um item pertence a uma única coleção e pode ser classificado por termos de diferentes taxonomias
* Metadados do tipo taxonomia atuam como ponte para termos
* Usuários têm permissões diferenciadas via funções e capacidades do WordPress

# 4.3 Manipulação de Dados e Acesso ao Banco WordPress

A manipulação de dados no plugin Tainacan é realizada usando uma combinação de funções nativas do WordPress e acesso direto ao banco de dados quando necessário.

### Funções Nativas do WordPress

Para a maioria das operações padrão, o Tainacan utiliza funções nativas do WordPress como:

- `wp_insert_post()`
- `get_post_meta()`
- `update_post_meta()`
- `wp_insert_term()`
- Entre outras.

Estas funções garantem compatibilidade com o ecossistema WordPress e beneficiam-se de recursos internos como cache e hooks.

### Acesso Direto ao Banco com `$wpdb`

Para cenários mais complexos ou ao lidar com tabelas personalizadas (ex: processos em segundo plano), o Tainacan utiliza acesso SQL direto via objeto `$wpdb`:

- Tabelas exemplo: `wp_tnc_bg_process`, `wp_tnc_log`
- Métodos exemplo: `$wpdb->insert()`, `$wpdb->get_results()`, `$wpdb->query()`

## 5. Sistema de Importação e Exportação

O Tainacan possui uma estrutura flexível para importação e exportação de dados. As operações são realizadas através de sessões e executadas de forma assíncrona.

### 5.1 Importação

* Importadores disponíveis: CSV, Flickr, YouTube, etc.
* Sessões criadas via `/importers/session`
* Upload de arquivo, mapeamento de metadados, execução em segundo plano
* Classe base: `class-tainacan-importer.php`
* Gerenciador: `class-tainacan-importer-handler.php`

### 5.2 Exportação

* Exportadores: CSV, Vocabulary CSV
* Sessões personalizáveis
* Classe base: `class-tainacan-exporter.php`
* Execução assíncrona com acesso a logs/resultados via endpoint
* Gerenciador: `class-tainacan-exporter-handler.php`

## 6. Arquitetura do Frontend

O frontend do Tainacan é uma aplicação Vue.js 3 que fornece uma interface para gerenciar repositórios digitais. Está implementado como uma Aplicação de Página Única (SPA) usando Vue.js 3, com roteamento gerenciado pelo Vue Router e gerenciamento de estado via Vuex. Comunica-se com o backend através da API REST do Tainacan.

Como SPA, o Tainacan carrega apenas uma única página HTML inicial e atualiza dinamicamente o conteúdo conforme o usuário navega, sem recarregar a página inteira. Isso proporciona uma experiência de usuário mais fluida e rápida. Um recurso chave do frontend é a busca facetada, que permite explorar intuitivamente as coleções combinando múltiplos filtros usando componentes interativos que atualizam resultados em tempo real. É implementado através de uma interface com **painéis de filtros interativos**, oferecendo tipos de controle como checkboxes, campos de texto e seletores de data, adaptados ao tipo de dado sendo filtrado.

Este documento apresenta o diagrama de fluxo de roteamento e dados da Aplicação de Página Única (SPA) do Tainacan, mostrando como ocorrem a inicialização, o roteamento e o fluxo de dados entre componentes.

![Diagrama de Inicialização e Roteamento](_assets/images/initialization_routing_front.png)

### 6.1 Localização dos Arquivos

Os arquivos do frontend estão localizados principalmente em:

- `/src/views/admin/` - Interface administrativa principal
- `/src/views/gutenberg-blocks/` - Blocos para o editor Gutenberg do WordPress

### 6.2 Componentes Principais

#### 6.2.1 Página Principal

O componente principal da aplicação é `src/views/admin/admin.vue`. Ele inicializa a aplicação Vue e define o layout principal, incluindo:
  - Menu de navegação lateral
  - Cabeçalho
  - Área de conteúdo principal
  - Gerenciamento de rotas

#### 6.2.2 Sistema de Roteamento

O sistema de roteamento é definido em `src/views/admin/js/router.js` e utiliza Vue Router para gerenciar a navegação.

Principais grupos de rotas:

**Páginas do repositório**:
   - `/home` - Página inicial
   - `/collections` - Lista de coleções
   - `/items` - Lista de itens
   - `/metadata` - Metadados
   - `/filters` - Filtros
   - `/taxonomies` - Taxonomias
   - `/activities` - Atividades
   - `/capabilities` - Permissões
   - `/importers` - Ferramentas de importação
   - `/exporters` - Ferramentas de exportação

### 6.3 Organização

As páginas da aplicação estão organizadas em:

- `/src/views/admin/pages/home-page.vue` - Página inicial
- `/src/views/admin/pages/lists/` - Páginas de listagem
- `/src/views/admin/pages/singles/` - Páginas de detalhe

Componentes reutilizáveis estão em `/src/views/admin/components/`:

- `/navigation/` - Componentes de navegação
- `/edition/` - Formulários de edição
- `/search/` - Componentes de busca
- `/other/` - Componentes diversos

### 6.4 Gerenciamento de Estado

O Tainacan utiliza Vuex para gerenciamento centralizado de estado. Os principais módulos são:

- **Collection**: Estado da coleção atual
- **Item**: Estado do item atual
- **Search**: Estado de buscas e filtros
- **Filter**: Filtros disponíveis
- **Metadata**: Metadados disponíveis

## 7. Referências e Contribuição

* 📚 **Wiki Oficial**: [https://tainacan.github.io/tainacan-wiki/](https://tainacan.github.io/tainacan-wiki/)
* 💻 **Código Fonte**: [https://github.com/tainacan/tainacan](https://github.com/tainacan/tainacan)
* 💬 **Fórum**: [https://tainacan.discourse.group/](https://tainacan.discourse.group/)
* ✉️ **Lista de Email**: [https://groups.google.com/g/tainacan](https://groups.google.com/g/tainacan)