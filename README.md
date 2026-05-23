# ⚙️ Katallo Backend — API da: Katallo | Catálogos Online

O backend da **Katallo** é responsável por toda a lógica de negócio da plataforma, incluindo:

- autenticação
- gerenciamento de lojas
- produtos
- categorias
- upload de imagens
- convites de administradores
- segurança multi-tenant

A API foi desenvolvida utilizando **Java + Spring Boot**, seguindo boas práticas de arquitetura backend moderna e preparada para evolução futura como SaaS.

⚠️ **Status do projeto: Em desenvolvimento ativo**

---

# 🚀 Sobre o Projeto

A Katallo é uma plataforma de catálogo online voltada para pequenas lojas que vendem principalmente via Whatsapp

O objetivo é transformar catálogos desorganizados em uma experiência moderna, profissional e escalável.

O backend fornece:

- API REST
- autenticação segura
- gerenciamento multi-loja
- isolamento de dados
- painel administrativo

---

# 🧱 Stack Tecnológica

## Backend

- Java 17
- Spring Boot
- Spring Web
- Spring Security
- Spring Data JPA
- Hibernate
- JWT Authentication
- Lombok
- Maven

## Banco de Dados

- MySQL

## Upload e Infraestrutura

- Cloudinary
- Docker

## Testes

- JUnit
- Mockito
- H2 Database
- Spring Security Test

---

# 🏗️ Arquitetura

O projeto segue arquitetura em camadas:

```txt
Controller → Service → Repository → Entity
```

Separações utilizadas:

- DTOs para entrada e saída
- Services para regras de negócio
- Repositories para persistência
- Controllers para camada HTTP

---

# 🏪 Arquitetura Multi-Loja+

A Katallo foi construída como uma plataforma multi-tenant.

Cada loja possui:

- produtos próprios
- categorias próprias
- administradores próprios
- identidade visual própria

A separação de dados ocorre através do `storeId`.

---

# 🔒 Segurança Multi-Tenant

Todos os endpoints administrativos validam:

- se o usuário pertence à loja
- se o recurso pertence à loja

Exemplo:

```txt
PUT /api/v1/admin/stores/{storeSlug}/products/{id}
```

O backend verifica:

- acesso do usuário à loja
- pertencimento do produto à loja

---

# 🔐 Autenticação

A autenticação é utilizada apenas no painel administrativo.

Método suportado:

- Google OAuth

Fluxo:

1. Usuário faz login com Google
2. Frontend recebe o ID Token
3. Backend valida o token Google
4. Backend cria/encontra usuário
5. Backend gera JWT próprio
6. Frontend utiliza JWT nas requisições autenticadas

---

# 🧠 Estratégia JWT

Características:

- JWT simples
- expiração de 24h
- sem refresh token no MVP

Header utilizado:

```txt
Authorization: Bearer {JWT}
```

Quando o token expira:

- backend retorna `401 Unauthorized`
- frontend remove token
- usuário é redirecionado para login

---

# 📂 Estrutura do Projeto

```txt
src/
├── main/
│   ├── java/
│   │   └── com/katallo/
│   │       ├── annotation/
│   │       ├── config/
│   │       ├── controller/
│   │       ├── domain/
│   │       ├── dto/
│   │       ├── exception/
│   │       ├── provider/
│   │       ├── repository/
│   │       ├── resolver/
│   │       ├── security/
│   │       ├── service/
│   │       ├── util/
│   │       └── KatalloPlatformApplication.java
│   └── resources/
│       └── application.yaml
│
├── test/
│   ├── java/
│   └── resources/
```

---

# 🌐 Endpoints da API

Prefixo padrão:

```txt
/api/v1
```

---

# 📦 Endpoints Públicos

## Loja

```txt
GET /api/v1/stores/{storeSlug}
```

## Categorias

```txt
GET /api/v1/stores/{storeSlug}/categories
```

## Produtos

```txt
GET /api/v1/stores/{storeSlug}/products
GET /api/v1/stores/{storeSlug}/products/slug/{productSlug}
GET /api/v1/stores/{storeSlug}/categories/{categorySlug}/products
```

## Convites

```txt
GET /api/v1/invites/{token}
```

---

# 🔐 Endpoints Administrativos

## Autenticação

```txt
POST /api/v1/auth/google
```

## Lojas

```txt
POST /api/v1/stores
PUT /api/v1/admin/stores/{storeSlug}
```

## Produtos

```txt
GET    /api/v1/admin/stores/{storeSlug}/products
POST   /api/v1/admin/stores/{storeSlug}/products
PUT    /api/v1/admin/stores/{storeSlug}/products/{id}
DELETE /api/v1/admin/stores/{storeSlug}/products/{id}
```

## Categorias

```txt
GET    /api/v1/admin/stores/{storeSlug}/categories
POST   /api/v1/admin/stores/{storeSlug}/categories
PUT    /api/v1/admin/stores/{storeSlug}/categories/{id}
DELETE /api/v1/admin/stores/{storeSlug}/categories/{id}
```

## Convites

```txt
POST   /api/v1/admin/stores/{storeSlug}/invites
GET    /api/v1/admin/stores/{storeSlug}/invites
DELETE /api/v1/admin/stores/{storeSlug}/invites/{inviteId}
```

---

# 👥 Sistema de Convites

Apenas usuários com role `OWNER` podem convidar administradores.

Fluxo:

1. OWNER envia convite
2. Backend gera token único
3. Frontend envia link ao convidado
4. Usuário aceita convite autenticado
5. Backend adiciona usuário à loja

---

# 🧑‍💼 Roles do Sistema

## OWNER

Permissões:

- gerenciar produtos
- gerenciar categorias
- convidar administradores
- remover membros
- alterar configurações da loja

## ADMIN

Permissões:

- gerenciar produtos
- gerenciar categorias

---

# 🖼️ Upload de Imagens

As imagens são armazenadas no Cloudinary.

Fluxo:

1. Frontend envia imagem
2. Backend valida:
   - tipo
   - tamanho
   - quantidade
3. Backend envia para Cloudinary
4. URL da imagem é salva no banco

Tipos permitidos:

- image/jpeg
- image/png
- image/webp

Limites:

- máximo de 5MB por imagem
- máximo de 8 imagens por produto

---

# 🗄️ Banco de Dados

Principais entidades:

- Store
- Product
- Category
- ProductImage
- User
- StoreUser
- StoreInvite

Relacionamento principal:

```txt
User ↔ Store
```

feito via tabela intermediária:

```txt
store_users
```

---

# 🔗 Slugs

A API utiliza slugs para URLs amigáveis.

Exemplos:

```txt
/minha-loja
/produto/vestido-floral-azul
/categoria/vestidos
```

Regras:

- minúsculo
- sem espaços
- sem caracteres especiais
- unicidade por loja

Os slugs são gerados automaticamente pelo backend.

---

# 🗑️ Soft Delete

Produtos e categorias utilizam soft delete.

Campo:

```txt
deletedAt
```

Isso permite:

- preservar histórico
- evitar inconsistências
- recuperação futura de dados

---

# 📄 Paginação

Todos os endpoints de listagem utilizam paginação.

Exemplo:

```txt
GET /products?page=0&size=20&sort=price,asc
```

Formato padrão:

```json
{
  "content": [],
  "page": 0,
  "size": 20,
  "totalElements": 134,
  "totalPages": 7,
  "last": false
}
```

---

# 🐳 Docker

O projeto possui suporte para containerização com Docker.

Arquivos presentes:

```txt
Dockerfile
docker-compose.yml
```

---

# ⚡ Boas Práticas Aplicadas

- arquitetura em camadas
- DTOs
- validação de entrada
- tratamento global de exceções
- logs estruturados
- versionamento de API
- isolamento multi-tenant
- segurança via JWT
- separação entre regras de negócio e controllers

---

# 📜 Logs

O sistema utiliza:

- SLF4J
- Logback

Exemplos:

```txt
INFO  StoreService   - Store created: storeId=15
WARN  SecurityFilter - Unauthorized access attempt
ERROR ProductService - Error saving product
```

---

# 🧪 Roadmap

## Curto Prazo

- [ ] Deploy oficial
- [ ] Rate limiting
- [ ] Cache

## Médio Prazo

- [ ] Sistema de planos
- [ ] Bloqueio automático de lojas
- [ ] Analytics com Google Analytics

## Longo Prazo

- [ ] Domínio próprio por loja
- [ ] Subdomínios automáticos
- [ ] Observabilidade

---

# ⚠️ Aviso

Este projeto ainda está em desenvolvimento e pode conter:

- funcionalidades incompletas
- mudanças estruturais
- possíveis bugs
- ajustes arquiteturais

Atualmente não é recomendado para produção em larga escala.

---

# 🤝 Contribuição

Sugestões, melhorias e feedbacks são bem-vindos.

---

# 📄 Licença

Este software é proprietário e não está licenciado para uso público, modificação ou distribuição.

© 2026 Gabriel Oliveira. Todos os direitos reservados.

---

# 👨‍💻 Autor

Desenvolvido por Gabriel Oliveira.
