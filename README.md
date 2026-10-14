 FleetMetrics - Sistema de Gestão de Frotas e Logística em SQL

Projeto desenvolvido com foco acadêmico e prático para consolidação de conceitos em **Banco de Dados Relacional e SQL avançado**, simulando o controle operacional e financeiro de uma rede de transportes e entregas.

---

## Tecnologias Utilizadas
* **Banco de Dados:** MySQL / MySQL Workbench
* **Conceitos Aplicados:** Modelagem Relacional (DER), Chaves Estrangeiras (`FOREIGN KEY`), Funções de Agregação, Joins (`LEFT JOIN`), Manipulação de Datas, `VIEW`s e `STORED PROCEDURE`s.

---

##Estrutura do Banco de Dados (`Schema`)
O banco de dados é composto por 5 tabelas principais interligadas por restrições de integridade referencial:

1. **`veiculos`**: Regista os dados da frota (placa, modelo, ano e status operacional).
2. **`motoristas`**: Regista a equipa de condutores (nome, CNH, telefone e status).
3. **`rotas`**: Define os trajetos disponíveis (origem, destino, distância em KM e tempo estimado).
4. **`entregas`**: Tabela transacional que conecta veículos, motoristas e rotas, registando datas, status e o valor do frete.
5. **`manutencoes`**: Regista o histórico de intervenções mecânicas e custos associados a cada veículo.

---

## Principais Funcionalidades e Consultas

### 1. Relatório de Desempenho de Motoristas
Consulta para apurar o volume de entregas e o total faturado por cada condutor, utilizando junções e agregações:
```sql
SELECT 
    m.nome,
    COUNT(e.id) AS total_entregas,
    ROUND(SUM(e.valor_frete), 2) AS valor_total_frete
FROM motoristas m
LEFT JOIN entregas e ON m.id = e.motorista_id
GROUP BY m.id, m.nome;
