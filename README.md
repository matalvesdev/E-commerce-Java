# E-commerce Java 🛒

API RESTful de e-commerce desenvolvida com Spring Boot e PostgreSQL, demonstrando relacionamentos JPA complexos e arquitetura de dados para sistemas de vendas online.

## 📋 Sobre o Projeto

Este projeto é uma aplicação backend completa de e-commerce que implementa um modelo de dados robusto com relacionamentos entre usuários, produtos, pedidos e endereços de cobrança.  A aplicação demonstra conceitos avançados de JPA/Hibernate, incluindo relacionamentos OneToOne, OneToMany, ManyToOne e ManyToMany.

## ✨ Funcionalidades

- 🏪 **Gerenciamento de Produtos** - Cadastro e controle de produtos
- 🏷️ **Sistema de Tags** - Categorização de produtos através de tags
- 👥 **Gerenciamento de Usuários** - Cadastro de usuários com endereço de cobrança
- 📦 **Gestão de Pedidos** - Criação e controle de pedidos
- 🛍️ **Itens de Pedido** - Relacionamento produtos-pedidos com preço e quantidade
- 📍 **Endereço de Cobrança** - Vinculação de endereços aos usuários

## 🛠️ Tecnologias Utilizadas

- **Java 21** - Linguagem de programação
- **Spring Boot 3.3.2** - Framework principal
- **Spring Data JPA** - Persistência de dados
- **PostgreSQL** - Banco de dados relacional
- **Hibernate** - ORM (Object-Relational Mapping)
- **Maven** - Gerenciador de dependências
- **Docker** - Containerização do banco de dados

## 🗄️ Modelo de Dados

### Entidades e Relacionamentos

```
UserEntity (tb_users)
├── userId:  UUID (PK)
├── fullName: String
└── billingAddress: BillingAddressEntity (OneToOne)

BillingAddressEntity (tb_billing_address)
├── billingAddressId: Long (PK)
├── address: String
├── number: String
└── complement: String

ProductEntity (tb_products)
├── productId: Long (PK)
├── productName: String
├── price:  BigDecimal
└── tags: List<TagEntity> (ManyToMany)

TagEntity (tb_tags)
├── tagId: Long (PK)
└── name: String (unique)

OrderEntity (tb_orders)
├── orderId: Long (PK)
├── total: BigDecimal
├── orderDate: LocalDateTime
├── user: UserEntity (ManyToOne)
└── items: List<OrderItemEntity> (OneToMany)

OrderItemEntity (tb_order_item)
├── id: OrderItemId (Composite Key)
├── salePrice: BigDecimal
└── quantity: Integer

OrderItemId (Embeddable)
├── order: OrderEntity
└── product: ProductEntity
```

### Relacionamentos

- **User → BillingAddress**:  `OneToOne` (Um usuário possui um endereço de cobrança)
- **Order → User**: `ManyToOne` (Vários pedidos pertencem a um usuário)
- **Order → OrderItem**: `OneToMany` (Um pedido possui vários itens)
- **OrderItem → Order**: `ManyToOne` (Vários itens pertencem a um pedido)
- **OrderItem → Product**: `ManyToOne` (Vários itens referenciam produtos)
- **Product → Tag**: `ManyToMany` (Produtos podem ter várias tags e vice-versa)

## 📦 Estrutura do Projeto

```
E-commerce-Java/
├── src/
│   └── main/
│       ├── java/
│       │   └── tech/
│       │       └── buildrun/
│       │           └── ecommerce/
│       │               ├── entities/
│       │               │   ├── BillingAddressEntity.java
│       │               │   ├── OrderEntity.java
│       │               │   ├── OrderItemEntity.java
│       │               │   ├── OrderItemId.java
│       │               │   ├── ProductEntity.java
│       │               │   ├── TagEntity.java
│       │               │   └── UserEntity.java
│       │               └── EcommerceApplication.java
│       └── resources/
│           ├── application.properties
│           └── data.sql
├── docker/
│   └── docker-compose.yml
├── collection/
│   └── [Postman Collection]
├── pom.xml
└── README. md
```

## 🚀 Como Executar

### Pré-requisitos

- Java 21 ou superior
- Maven 3.6+
- Docker e Docker Compose

### Passo 1: Clone o repositório

```bash
git clone https://github.com/matalvesdev/E-commerce-Java.git
cd E-commerce-Java
```

### Passo 2: Inicie o banco de dados PostgreSQL com Docker

```bash
cd docker
docker-compose up -d
```

Isso irá criar um container PostgreSQL com as seguintes configurações:
- **Database**: ecommercedb
- **User**: myuser
- **Password**: secret
- **Port**: 5432

### Passo 3: Execute a aplicação

```bash
# Volte para o diretório raiz
cd ..

# Execute com Maven Wrapper
./mvnw spring-boot:run

# Ou compile e execute o JAR
./mvnw clean package
java -jar target/ecommerce-0.0.1-SNAPSHOT. jar
```

A aplicação estará disponível em:  `http://localhost:8080`

## 📊 Dados de Exemplo

A aplicação possui um arquivo `data.sql` que popula o banco com dados iniciais:

### Produtos
| ID | Nome | Preço |
|----|------|-------|
| 1 | Computer | R$ 4.500,50 |
| 2 | Smartphone | R$ 2.000,00 |
| 3 | Mouse | R$ 200,00 |

### Tags
| ID | Nome |
|----|------|
| 1 | Eletronics |
| 2 | Home |
| 3 | Apple |

### Relacionamentos Produto-Tag
- Computer → Eletronics
- Smartphone → Apple, Eletronics
- Mouse → Eletronics

## ⚙️ Configuração

### application.properties

```properties
spring.application.name=ecommerce

# Database Configuration
spring.datasource.url=jdbc:postgresql://localhost:5432/ecommercedb
spring.datasource.username=myuser
spring.datasource.password=secret
spring.datasource.driver-class-name=org.postgresql.Driver

# JPA/Hibernate Configuration
spring.jpa.hibernate.ddl-auto=update
spring.jpa.database-platform=org.hibernate.dialect.PostgreSQLDialect
spring.jpa.show-sql=true

# Data Initialization
spring.sql.init.mode=always
spring.jpa.defer-datasource-initialization=true
```

### Docker Compose

```yaml
services:
  postgres:
    image: 'postgres:latest'
    environment:
      - 'POSTGRES_DB=ecommercedb'
      - 'POSTGRES_PASSWORD=secret'
      - 'POSTGRES_USER=myuser'
    ports: 
      - '5432:5432'
```

## 🔑 Características Técnicas

### Chave Composta (Composite Key)
O projeto utiliza uma chave composta `OrderItemId` para a entidade `OrderItemEntity`, implementando o padrão de tabela associativa com atributos adicionais.

```java
@Embeddable
public class OrderItemId {
    @ManyToOne
    private OrderEntity order;
    
    @ManyToOne
    private ProductEntity product;
}
```

### Cascade e Fetch Strategy
- **BillingAddress**: `CascadeType.ALL` com `FetchType.EAGER`
- **OrderItems**: `CascadeType.ALL` para persistência automática

### Constraints
- **CPF/Email únicos** nas tabelas de usuários (se implementado)
- **Tag name único** para evitar duplicação
- **Unique constraint** na tabela `tb_products_tags`

## 🧪 Testes

```bash
./mvnw test
```

## 📝 Notas de Desenvolvimento

- A aplicação utiliza **Hibernate** com `ddl-auto=update` para criação automática do schema
- O script `data.sql` é executado automaticamente na inicialização
- UUID é utilizado como identificador do usuário para maior segurança
- BigDecimal é usado para valores monetários, garantindo precisão
- Relacionamento bidirecional é mantido entre Order e OrderItem

## 🎯 Próximos Passos

- [ ] Implementar endpoints REST para CRUD de produtos
- [ ] Adicionar autenticação e autorização com Spring Security
- [ ] Implementar validação de dados com Bean Validation
- [ ] Adicionar testes unitários e de integração
- [ ] Implementar DTOs para transferência de dados
- [ ] Adicionar documentação Swagger/OpenAPI
- [ ] Implementar paginação e filtros

## 👨‍💻 Autor

Desenvolvido por [matalvesdev](https://github.com/matalvesdev)

## 📄 Licença

Este projeto está sob a licença MIT.  Veja o arquivo LICENSE para mais detalhes.

---

⭐ Se este projeto foi útil para você, considere dar uma estrela! 
