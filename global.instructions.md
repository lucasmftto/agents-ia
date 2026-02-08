# GitHub Copilot – Instruction File for This Repository

Estas instruções definem como o Copilot deve gerar código, testes, arquitetura, padrões e boas práticas para este repositório.

---

# 📌 Tecnologias utilizadas neste repositório

- Java 17 ou Java 21
- Spring Boot
- Spring Web
- Spring Data MongoDB
- Lombok
- Apache Artemis (JMS)
- Spring Cloud OpenFeign
- AssertJ
- Testcontainers
- JUnit 5
- Mockito (somente quando necessário para integrações externas)
- Jackson

---

# 📌 Padrões de Código

- Utilize Java idiomático, claro e moderno.
- Utilize Lombok amplamente para reduzir boilerplate.
- Utilize constructor injection.
- Evite `var` quando reduzir clareza.
- Utilize imutabilidade sempre que possível.
- Utilize DTOs específicos para request e response.
- Evite acoplamento excessivo.
- Preferir classes pequenas, coesas e com responsabilidade única.
- Utilizar `final` sempre que fizer sentido.

---

# 📌 Padrões de Arquitetura

- Controllers: expõem API e não contêm lógica de negócio.
- Services: contêm lógica de negócio.
- Repositories: persistência usando MongoRepository.
- Messaging: produtores e listeners do Artemis.
- Integration: Feign Clients para APIs externas.
- Entities: modelos persistidos no Mongo.
- DTO/Resources: classes de entrada/saída.

**Regras gerais:**

- Não colocar lógica em Feign Clients.
- Não criar classes gigantes (God Objects).
- Seguir modularidade clara.

---

# 📌 Spring

- Utilize `@RestController`, `@Service`, `@Repository`, etc., onde apropriado.
- Utilize sempre Bean Validation.
- Evite expor entidades diretamente.
- Utilize `ResponseEntity` quando necessário.
- Utilizar `@ConfigurationProperties` para configurações.

---

# 📌 MongoDB

- Utilize `MongoRepository`.
- Evitar queries manuais complexas.
- Definir índices onde necessário.
- Modelar documentos corretamente.

---

# 📌 Artemis (JMS)

- Utilize `JmsTemplate` para envio.
- Utilize `@JmsListener` para consumo.
- Seguir padrões de nome para filas/tópicos.
- Tratar erros adequadamente.

---

# 📌 Feign Client

- Utilizar `@FeignClient(name = "...", url = "...")`.
- Não adicionar lógica de negócio na interface.
- Criar DTOs específicos para requests/responses.
- Configurar timeouts e fallbacks quando necessário.

---

# 🧪 TESTES – Regras Gerais

- NÃO mockar repositórios.
- NÃO mockar serviços internos da aplicação.
- Mockar **somente** integrações externas:
    - Feign Clients
    - Mensageria externa
- Sempre preferir testes com lógica real, usando banco real via Testcontainers.
- Utilizar AssertJ sempre que possível.
- Testes devem validar efeitos reais no banco de dados e nas entidades.

---

# 🧪 Preferência 1 – Testes de Controller (MockMvc)

O Copilot deve gerar testes de controller seguindo as regras:

- Usar `@SpringBootTest`.
- Usar `@AutoConfigureMockMvc`.
- Não mockar repositórios ou serviços.
- Configurar Testcontainers com `TestContainerConfig`.
- Criar cenário real no banco usando `repository.save(...)`.
- Fazer requests reais usando MockMvc.
- Validar status, corpo e persistência.
- Validar lógica executada completamente.

**Modelo oficial que deve ser seguido:**

```java
@AutoConfigureMockMvc
@SpringBootTest(classes = Application.class,
        webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
@ContextConfiguration(initializers = TestContainerConfig.class)
@ActiveProfiles("test")
@TestPropertySource(locations = "classpath:application-test.yml")
class MyControllerTest {

    @Autowired
    private MockMvc mvc;

    @Autowired
    private MyRepository repository;

    @Autowired
    private ObjectMapper mapper;

    @BeforeEach
    @AfterEach
    void cleanup() {
        repository.deleteAll();
    }

    @Test
    void shouldMakeActionRequest() throws Exception {
        // Cenário real
        repository.save(...);

        mvc.perform(post("/v1/my/action-request")
                .content(mapper.writeValueAsString(...))
                .contentType(MediaType.APPLICATION_JSON))
                .andExpect(status().isOk());

        // Validação real
        ConsolidatedEntity entity = repository.findById("194881").orElseThrow();
        assertThat(entity.getTests()).isNotEmpty();
    }
}

```

# 🧪 Preferência 2 – Testes de Serviço (Service Layer)
O Copilot deve gerar testes de serviço seguindo estas diretrizes:
- Não mockar repository.
- Não mockar services internos.
- Rodar com @SpringBootTest.
- Utilizar Testcontainers como banco real.
- Mockar somente Feign Clients ou integrações externas.
- Manipular dados reais no MongoDB.
- Validar estado final da entidade salva.
- Rodar verificações pós-teste (ex.: duplicidades, integridade).
- Manter o mesmo padrão de organização, setup e teardown do exemplo abaixo.

**Modelo oficial que o Copilot deve seguir:**

```java

@Slf4j
@SpringBootTest(classes = Application.class)
@ContextConfiguration(initializers = TestContainerConfig.class)
@ActiveProfiles("test")
@TestPropertySource("classpath:application-test.yml")
class MyServiceTest {

    @Autowired
    private MyRepository repository;

    @Autowired
    private MyService service;

    @MockBean
    private MyIntegrationFeignClient myIntegrationFeignClient;

    @MockBean
    private erpServiceFeignClient erpServiceFeignClient;

    @BeforeEach
    void before() {
        repository.deleteAll();
    }

    @AfterEach
    void after() {
        verifyNoDuplicates();
        verifyNoDuplicates();
    }

    @Test
    void shouldSaveTestAndChangeStatus() {
        ConsolidatedEntity entity = new ConsolidatedEntity();
        entity.setImpNumber("194881");
        repository.save(entity);

        service.setmyTest(...);

        ConsolidatedEntity saved = repository.findById("194881").orElseThrow();
        assertThat(saved.getStatus()).isEqualTo(ProcessStatus.MY_ANALYSIS);
    }
}

```

## 4. Testing Guidelines (JUnit 5)

Comprehensive guidelines for writing effective and reliable tests with JUnit 5.

### 4.1. General Best Practices

* **JUnit 5 Annotations:**
  * Use standard JUnit 5 annotations: `@Test`, `@BeforeEach`, `@AfterEach`, `@BeforeAll`, `@AfterAll`.
  * Organize tests using `@DisplayName` for readable test names, `@Nested` for structuring related tests, and `@Tag` for categorizing tests (e.g., "unit", "integration", "slow").
* **AAA Pattern:**
  * Strictly follow the **AAA (Arrange → Act → Assert) pattern** within each test method:
    * **Arrange:** Set up test data, mocks, and the system under test (SUT).
    * **Act:** Execute the method or action being tested.
    * **Assert:** Verify the expected outcomes using assertions (e.g., `assertEquals`, `assertTrue`, `assertThrows`, `verify`).
* **Descriptive Method Names:**
  * Use clear and descriptive test method names that explain what is being tested and the expected outcome.
  * Naming convention: `[methodUnderTest]_[scenario]_[expectedOutcome]` or `should[ExpectedOutcome]When[Scenario]`.
    * Example: `createUser_validInput_returnsNewUser()` or `shouldReturnNewUserWhenValidInput()`.

### 4.2. Test Coverage Guidelines

* **Coverage Targets:**
  * Aim for **90%+ code coverage** on the **Domain** and **Application (Service/Use Case)** layers. These layers contain the core business logic and should be thoroughly tested.
  * Infrastructure and Interface layers may have slightly lower targets but should still have adequate coverage for critical paths.
* **Test Scenarios:**
  * Write tests for both the **happy path** (expected successful execution) and **edge cases**.
  * **Edge cases** include: invalid inputs, empty collections, boundary conditions, error conditions, null values, concurrency issues (where applicable).
* **Validation & Branches:**
  * **Validate all validations:** Ensure that input validation logic is thoroughly tested, confirming correct error responses for invalid data.
  * **Test all branches and decision points:** Use test cases to cover every `if/else`, `switch` case, and loop condition.
* **Exclusions:**
  * **Do not test simple POJOs** (Plain Old Java Objects) like DTOs, records, or entities that only contain getters/setters and have no custom logic. Their functionality is often guaranteed by the language or frameworks.
  * **Do not test generated code** (e.g., Lombok-generated methods, framework proxies).
* **Mocking External Services:**
  * For unit tests, **mock all external dependencies and services** (e.g., REST clients, database repositories, message queues, file systems). Use mocking frameworks like Mockito.
  * This ensures that unit tests are fast, isolated, and deterministic. Integration tests can then verify the interaction with real external services.

---