> References:
> https://backstage.io
> https://github.com/backstage/backstage



[Backstage](https://backstage.io/) is an open platform for building developer portals.

Out of the box, Backstage includes:
- software catalog
- software templates
- techDocs
- open source plugins

# Architecture

- The core Backstage UI
- The UI plugins and their backing services
- Databases

Plugins can take three forms: 

- Standalone - run entirely in the browser
- Service backed - make API requests to a service which is within the purview of the organisation running Backstage.
- Third-party backed - similar to service backed plugins.

Cache

The Backstage backend and its built-in plugins are also able to leverage cache stores as a means of improving performance or reliability. Plugins receive logically separated cache connections, which are powered by [Keyv](https://github.com/lukechilds/keyv) under the hood.

# Core Features

### Software Catalog

There are 3 ways to add components to the catalog: 

- Manually register components 
- Creating new components through Backstage 
- Integrating with an [external source](https://backstage.io/docs/features/software-catalog/external-integrations)

The catalog forms a hub of sorts, where entities are ingested from various authoritative sources and held in a database, subject to automated processing, and then presented through an API for quick and easy access by Backstage and others. 

The most common source is [YAML files](https://backstage.io/docs/features/software-catalog/descriptor-format) on a standard format, living in version control systems near the source code of systems that they describe. Those files are registered with the catalog and maintained by the respective owners. The catalog makes sure to keep itself up to date with changes to those files.

### Kubernetes

The feature is made up of two plugins: [`@backstage/plugin-kubernetes`](https://github.com/backstage/backstage/tree/master/plugins/kubernetes) and [`@backstage/plugin-kubernetes-backend`](https://github.com/backstage/backstage/tree/master/plugins/kubernetes-backend). The frontend plugin exposes information to the end user in a digestible way, while the backend wraps the mechanics to connect to Kubernetes clusters to collect the relevant information.

There are two ways to surface your Kubernetes components as part of an entity. The label selector takes precedence over the annotation/service id.

### Software Templates

Creates Components inside Backstage. Loads skeletons of code, templates in some variables, and then publishes the template to some locations like GitHub or GitLab.

### Backstage Search

- bring your own search engine
- extend it by creating collators for easily indexing content from plugins and other sources
- create composable search page experiences
- customize the look and feel of each search result

Search Concepts:
- Search Engines
- Query Translators
- Documents and Indices
- Collators
- Decorators
- The Scheduler
- The Search Page
- Search Context and Components

### TechDocs

Engineers write their documentation in Markdown files which live  together with their code - and with little configuration get a  nice-looking doc site in Backstage.

# Integrations

Integrations allow Backstage to read or publish data using external providers such as GitHub, GitLab, Bitbucket, LDAP, or cloud providers.

# Plugins

Backstage is a single-page application composed of a set of plugins.

Each plugin is treated as a self-contained web app and can include almost any type of content. Plugins all use a common set of platform APIs and reusable UI components. Plugins can fetch data from external sources using the regular browser APIs or by depending on external modules to do the work.

# Configuration

Configuration is stored in YAML files where the defaults are `app-config.yaml` and `app-config.local.yaml` for local overrides. Other sets of files can by loaded by passing `--config <path>` flags.

# Authentication

Sign-in and identification of users, as well as delegating access to third-party resources. Supported:

    Atlassian
    Auth0
    Azure
    Bitbucket
    Cloudflare Access
    GitHub
    GitLab
    Google
    Google IAP
    Okta
    OneLogin
    OAuth2Proxy

Backstage can also *authorize* specific data, APIs, or  interface actions - meaning that Backstage has the ability to enforce  rules about what type of access is allowed for a given user of a system. This is done via The permission framework.

# Deploying Backstage











---

#### Pros:

- many plugins
- many integrations

#### Cons:

- pure frontend, written in Typescript and React
- backed plugins and caching configuration
- Backend Services (MVP) feature is not implemented yet 
- early stage, alpha version
- kubernetes plugins doesn't support all resources
- complicated catalog file structure
- cumbersome and hard to customize
- will be hard to get the teams to align with having the catalog infos
- yet another place to stick docs and other stuff
- requires a lot of work to make it work as intended
- haven't seen articles and reviews not from Spotify staff