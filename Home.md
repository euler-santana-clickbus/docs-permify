# Bem-vindo à Documentação do Permify AuthZ

Este repositório contém a documentação completa da arquitetura de autorização granular utilizando **Permify** como serviço centralizado de AuthZ (Autorização), complementando o **Keycloak** para AuthN (Autenticação).

## 🚀 Visão Rápida

| Componente | Responsabilidade | Tecnologia |
|:---|:---|:---|
| **Keycloak** | "Quem é você?" | Autenticação, JWT, PKCE |
| **Permify** | "O que você pode fazer?" | Autorização fine-grained, ReBAC |
| **BFF** | Gateway de API | NestJS + Guards |
| **Frontend** | UI condicional | Next.js + Hooks |

## 📋 Páginas Principais

### Visão Geral
- **[PRD — Permify AuthZ](PRD---Permify-AuthZ)** - Requisitos do produto, objetivos e métricas
- **[RFC — Decisões de Design](RFC---Decisões-de-Design)** - Arquitetura, governança e padrões

### Arquitetura
- **[Arquitetura AuthN + AuthZ](Arquitetura-AuthN-+-AuthZ)** - Fluxo completo de autenticação e autorização
- **[Hierarquia Org → Company → Module](Hierarquia-Org-→-Company-→-Module)** - Modelo de dados e relacionamentos

### Desenvolvimento
- **[Integração NestJS + Spring Boot](Integração-NestJS-+-Spring-Boot)** - Guias práticos de implementação
- **[Exemplos Práticos](Exemplos-Práticos)** - Casos de uso reais com código
- **[SDKs Oficiais](SDKs-Oficiais)** - Bibliotecas para todas as linguagens

### Operação
- **[Configuração do Permify](Configuração-do-Permify)** - Setup, deploy e operação
- **[Política de Permissões](Política-de-Permissões)** - Regras e convenções obrigatórias
- **[Troubleshooting](Troubleshooting)** - Debug e problemas comuns

## 🏗️ Arquitetura em 3 Camadas

```
┌─────────────────────────────────────────────────────────────────┐
│                         Frontend                                │
│  - Login via Keycloak (PKCE)                                    │
│  - Renderização condicional (UX apenas)                          │
└────────────────────┬────────────────────────────────────────────┘
                     │ JWT + x-org-id
                     ▼
┌─────────────────────────────────────────────────────────────────┐
│                      BFF (NestJS)                              │
│  - Valida JWT (AuthN)                                           │
│  - Consulta Permify (AuthZ)                                      │
│  - Expor /api/v4/permissions/me                                 │
└────────────────────┬────────────────────────────────────────────┘
                     │
                     ▼
┌─────────────────────────────────────────────────────────────────┐
│                    Permify + Postgres                           │
│  - Engine de decisão ReBAC                                       │
│  - Schema de permissões                                         │
│  - Tuples (relações)                                            │
└─────────────────────────────────────────────────────────────────┘
```

## 🎯 Objetivos Principais

1. **Centralizar 100%** das decisões de autorização no Permify
2. **Suportar multi-tenancy** com hierarquia flexível
3. **Implementar query filtering** para evitar vazamento de dados
4. **Garantir fail-closed** (deny por padrão)
5. **Permitir self-service** para times de produto

## 📊 SDKs Disponíveis

| Linguagem | SDK | Instalação |
|:---|:---|:---|
| **Node.js / TypeScript** | [@permify/permify-node](https://github.com/Permify/permify-node) | `npm install @permify/permify-node` |
| **Java** | [permify-java](https://github.com/Permify/permify-java) | Maven/Gradle |
| **Go** | [permify-go](https://github.com/Permify/permify-go) | `go get github.com/Permify/permify-go` |
| **Python** | [permify-python](https://github.com/Permify/permify-python) | `pip install permify` |
| **Ruby** | [permify-ruby](https://github.com/Permify/permify-ruby) | `gem install permify` |
| **PHP** | [permify-php](https://github.com/Permify/permify-php) | Composer |
| **C#** | [permify-csharp](https://github.com/Permify/permify-csharp) | NuGet |
| **TypeScript (Frontend)** | [@permify/permify-typescript](https://github.com/Permify/permify-typescript) | `npm install @permify/permify-typescript` |

## 🚦 Getting Started

### 1. Setup Local

```bash
# Clone o repositório
git clone https://github.com/euler-santana-clickbus/docs-permify.git
cd docs-permify

# Inicie o Permify com Docker
docker-compose up -d

# Configure as variáveis de ambiente
export PERMIFY_URL=http://localhost:3476
export PERMIFY_TENANT_ID=dev
```

### 2. Exemplo Rápido (Node.js)

```typescript
import * as permify from '@permify/permify-node';

const client = permify.grpc.newClient({
  endpoint: 'http://localhost:3476',
  tenantId: 'dev'
});

// Verificar permissão
const result = await client.permissions.check({
  entity: { type: 'module', id: 'insights' },
  permission: 'view',
  subject: { type: 'user', id: 'alice' }
});

console.log(result.can); // true ou false
```

### 3. Exemplo Rápido (Java)

```java
PermifyClient client = new PermifyClient("http://localhost:3476", "dev");

CheckResponse resp = client.check(
  Entity.newBuilder().setType("module").setId("insights").build(),
  "view",
  Subject.newBuilder().setType("user").setId("alice").build()
);

System.out.println(resp.getCan()); // true ou false
```

## 📈 Métricas de Performance

| Métrica | Target |
|:---|:---|
| p50 latência (check) | < 5ms |
| p95 latência (check) | < 15ms |
| p99 latência (check) | < 50ms |
| Cache hit rate | > 80% |

## 🔗 Links Úteis

- **[Permify Documentation](https://permify.co/docs)** - Documentação oficial
- **[Playground](https://play.permify.co)** - Testar schemas online
- **[GitHub](https://github.com/Permify/permify)** - Código fonte
- **[Discord](https://discord.gg/permify)** - Comunidade

## 🤝 Contribuição

1. Faça fork do repositório
2. Crie uma branch: `git checkout -b feature/nova-funcionalidade`
3. Faça commit: `git commit -m 'Add: nova funcionalidade'`
4. Push: `git push origin feature/nova-funcionalidade`
5. Abra um Pull Request

## 📞 Contato

- **Platform Team** - `platform@clickbus.com`
- **Slack** - `#permify-authz`
- **Issues** - [GitHub Issues](https://github.com/euler-santana-clickbus/docs-permify/issues)

---

**Platform Team · 2026**