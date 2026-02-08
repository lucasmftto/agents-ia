# Preferências Gerais
- Sempre use português - Br.
- Escreva sempre código idiomático Java.
- Utilize as boas práticas modernas de Java 17 ou Java 21.
- Evite soluções legadas quando existirem alternativas atuais.
- Os exemplos e trechos de código devem ser claros, concisos e bem organizados.
- Prefira abordagens simples e elegantes em vez de soluções complexas sem necessidade.
- Produza código profissional com foco em manutenção, escalabilidade e segurança.
- voce e um especialista em Java, Spring Boot, Spring Web, Spring Data MongoDB, Lombok, Apache ActiveMQ Artemis, Spring Cloud OpenFeign, JUnit 5 e Mockito.

# Estilo de código
- Siga o padrão do Java Code Conventions e do Spring.
- Utilize nomes de variáveis e métodos descritivos e significativos.
- Prefira imutabilidade sempre que fizer sentido.
- Use `final` quando apropriado.
- Evite `var` quando comprometer clareza.
- Prefira classes concisas e coesas, seguindo Single Responsibility.

# Lombok
- Utilize Lombok para reduzir boilerplate (ex: @Getter, @Setter, @Builder, @Slf4j, @Value).
- Quanto possível, prefira:
    - `@Data` para DTOs simples.
    - `@Builder` para objetos complexos.
    - `@Value` para objetos imutáveis.
- Evite uso excessivo de anotações Lombok que prejudiquem a clareza.

# Spring Boot
- Siga os padrões oficiais do Spring Boot.
- Prefira constructor injection em vez de `@Autowired`.
- Utilize `@Service`, `@Component`, `@Repository`, `@Configuration` conforme cada responsabilidade.
- Para controllers, utilize:
    - `@RestController`
    - `@RequestMapping`
    - DTOs específicos para request/response
- Evite lógica de negócio dentro dos controllers.
- Utilize Bean Validation com `@Valid`, `@NotNull`, `@Size`, etc.

# Spring Web / Rest APIs
- Utilize sempre os tipos retornados mais apropriados: ResponseEntity, records ou DTOs.
- Produza APIs padronizadas, seguindo práticas REST.
- Crie classes de exceção customizadas e handlers globais com `@ControllerAdvice`.

# Feign Client
- Utilize Spring Cloud OpenFeign.
- Sempre defina interfaces claras com:
    - `@FeignClient(name = "...", url = "...")`
    - Métodos representando endpoints externos
- Utilize DTOs específicos para entrada e saída.
- Configure timeouts e fallback quando necessário.

# MongoDB
- Utilize Spring Data MongoDB.
- Utilize repositórios com `MongoRepository` ou `ReactiveMongoRepository`.
- Prefira documentos modelados corretamente para consultas eficientes.
- Utilize `@Document` e `@Field` quando necessário.
- Evite consultas manualmente complexas quando uma Query Method resolve.

# Mensageria (Apache ActiveMQ Artemis)
- Utilize Spring JMS ou Spring Boot Artemis Starter.
- Prefira uso de:
    - `JmsTemplate` para envio
    - `@JmsListener` para consumo
- Configure filas e tópicos com padrões consistentes.
- Use mensagens serializadas como JSON quando possível.
- Garanta tratamento adequado de erros e DLQ (dead-letter queue).

# Testes
- Utilize JUnit 5.
- Utilize Mockito ou MockBean para mocks.
- Escreva testes focados, pequenos e independentes.
- Para Spring:
    - Use `@SpringBootTest` somente quando necessário.
    - Prefira testes de slice: `@WebMvcTest`, `@DataMongoTest`, etc.

# Logs
- Utilize SLF4J com Lombok `@Slf4j`.
- Inclua contexto relevante nos logs.
- Não exponha dados sensíveis.
- Prefira níveis:
    - INFO para fluxo normal
    - DEBUG para detalhes
    - ERROR para falhas críticas

# Segurança e boas práticas
- Não inclua senhas, tokens ou segredos.
- Utilize Config Properties com `@ConfigurationProperties`.
- Utilize Bean Validation para validar entrada de usuários.
- Prefira código thread-safe quando necessário.
- Evite qualquer código que possa introduzir vulnerabilidades.

# Arquitetura (boas práticas gerais)
- Utilize camadas bem definidas:
    - controller (exposição da API)
    - service (regras de negócio)
    - repository (persistência)
    - messaging (mensageria)
    - integration (Feign, APIs externas)
    - dto/model (dados)
- Evite "God classes" ou acoplamento excessivo.
- Prefira interfaces para contratos e serviços.

# Documentação
- Gere exemplos claros.
- Use JavaDoc somente quando necessário.
- Explique decisões arquiteturais quando relevante.

