# Automação de Testes para a API GoRest

Este repositório contém a automação de testes para a API REST [GoRest](https://gorest.co.in/), incluindo a validação de schema, verificação de status, interpretação de datas e a implementação de práticas de DevOps como testes de carga e Continuous Testing.

## 1. Ferramenta Escolhida: Postman

**Por que Postman?**

Postman é uma ferramenta robusta e amplamente utilizada para o desenvolvimento e automação de testes de APIs RESTful. A escolha do Postman foi baseada em:

- **Facilidade de Uso:** Interface intuitiva que facilita a criação e execução de testes sem necessidade de escrever scripts complexos.
- **Testes Automatizados:** Suporte para escrita de testes automatizados usando JavaScript na aba "Tests".
- **Validação de Schema:** Permite a validação de schemas JSON para garantir que as respostas da API estejam no formato esperado.
- **Integração com CI/CD:** Facilmente integrável com pipelines de CI/CD para Continuous Testing.

## 2. Casos de Uso para Testes

Os casos de uso para os testes nesta API incluem:

1. **Validação da Estrutura de Dados:** Garantir que a resposta da API siga o schema esperado (campos, tipos de dados).
2. **Verificação de Status:** Certificar-se de que todos os itens retornados têm o status "completed".
3. **Validação de Datas:** Analisar e validar o formato e os valores das datas no campo `due_on`.
4. **Testes de Limite:** Verificar o comportamento da API quando os parâmetros de busca são variados (ex.: limites de paginação).
5. **Testes de Autenticação:** Certificar-se de que as respostas da API estejam protegidas por autenticação quando necessário.

## 3. Automação de Testes com Postman

### 3.1. Validação de Schema

```javascript
pm.test("Valida o schema da resposta", function () {
    var schema = {
        "type": "array",
        "items": {
            "type": "object",
            "properties": {
                "id": { "type": "integer" },
                "user_id": { "type": "integer" },
                "title": { "type": "string" },
                "due_on": { "type": "string", "format": "date-time" },
                "status": { "type": "string" }
            },
            "required": ["id", "user_id", "title", "due_on", "status"]
        }
    };
    pm.response.to.have.jsonSchema(schema);
});

/javascript```

### 3.2. Validação de Status "Completed"

```javascript
pm.test("Verificar se todos os itens têm status 'completed'", function () {
    let todos = pm.response.json();
    let totalCompleted = todos.filter(todo => todo.status === "completed").length;
    let totalPending = todos.length - totalCompleted;

    pm.expect(totalPending).to.equal(0, `Nem todos os itens têm status 'completed': ${totalPending} item(s) com status diferente de 'completed'`);
    console.log(`Total de itens com status 'completed': ${totalCompleted}`);
});
/javascript```

### 3.3. Validação do Campo due_on
```javascript
Copiar código
pm.test("due_on is a valid date-time", function () {
    let todos = pm.response.json();
    todos.forEach(todo => {
        pm.expect(todo.due_on).to.match(
            /^\d{4}-\d{2}-\d{2}T\d{2}:\d{2}:\d{2}\.\d{3}\+\d{2}:\d{2}$/, 
            `Invalid date format in 'due_on': ${todo.due_on}`
        );
    });
});
/javascript```

## 4. DevOps, CI/CD

### 4.1. Implementação de Testes de Carga

Para implementar testes de carga na API GoRest, recomendaria o uso do **Apache JMeter**:

**Por que JMeter?**

JMeter é uma ferramenta de código aberto amplamente usada para realizar testes de carga e desempenho em APIs. Ele permite simular múltiplos usuários simultâneos e medir o comportamento da API sob carga.

**Como Configurar:**

- **Definir um Plano de Teste:** Crie um plano de teste no JMeter que simule múltiplos usuários fazendo requisições à API `/public/v2/todos`.
- **Configurações do Thread Group:** Ajuste as configurações de Thread Group para variar o número de usuários simultâneos e o tempo de ramp-up.
- **Monitoramento:** Monitore métricas como tempo de resposta, throughput, e taxa de erro para identificar gargalos ou pontos de falha.

### 4.2. Continuous Testing

Para implementar Continuous Testing, integraria os testes automatizados do Postman com um pipeline de CI/CD:

**Ferramentas Usadas:** GitHub Actions, Jenkins, ou outra plataforma de CI/CD.

**Pipeline de Testes:**

- **Postman Collection Runner:** Configure o Postman Collection Runner no pipeline para rodar os testes automaticamente em cada push ou pull request.
- **Newman CLI:** Use o Newman (CLI do Postman) para executar os testes no ambiente de CI/CD.
- **Notificações:** Integre notificações para alertar a equipe sobre falhas nos testes, permitindo correções rápidas.

Essa abordagem garante que a API seja testada continuamente em diferentes etapas do desenvolvimento, minimizando a introdução de bugs e garantindo a estabilidade da API.
