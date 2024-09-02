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
