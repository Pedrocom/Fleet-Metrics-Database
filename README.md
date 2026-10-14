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
```
SELECT 
    m.nome,
    COUNT(e.id) AS total_entregas,
    ROUND(SUM(e.valor_frete), 2) AS valor_total_frete
FROM motoristas m
LEFT JOIN entregas e ON m.id = e.motorista_id
GROUP BY m.id, m.nome;
```


### 2. Análise Financeira da Frota (Faturamento vs. Manutenção)
```
Cruzamento de dados para calcular a receita de fretes frente aos custos de manutenção de cada veículo da frota, garantindo uma visão de saldo operacional:
SELECT 
    v.placa, 
    v.modelo,
    COUNT(e.id) AS total_entregas,
    ROUND(SUM(e.valor_frete), 2) AS valor_total,
    ROUND(SUM(mn.custo), 2) AS valor_manutencao,
    ROUND(SUM(e.valor_frete) - SUM(mn.custo), 2) AS saldo_operacional
FROM veiculos v
LEFT JOIN entregas e ON v.id = e.veiculo_id
LEFT JOIN manutencoes mn ON v.id = mn.veiculo_id
GROUP BY v.id, v.placa, v.modelo;
```
### 3. Abstração com VIEW 
Criação de uma tabela virtual para simplificar e reapresentar a consulta financeira consolidada da frota:
```
CREATE VIEW vw_saldo_veiculos AS
SELECT 
    v.placa, 
    v.modelo,
    COUNT(e.id) AS total_entregas,
    ROUND(SUM(e.valor_frete), 2) AS valor_total,
    ROUND(SUM(mn.custo), 2) AS valor_manutencao,
    ROUND(SUM(e.valor_frete) - SUM(mn.custo), 2) AS saldo_operacional
FROM veiculos v
LEFT JOIN entregas e ON v.id = e.veiculo_id
LEFT JOIN manutencoes mn ON v.id = mn.veiculo_id
GROUP BY v.id, v.placa, v.modelo;
```
### 4. Automação com STORED PROCEDURE
Procedimento armazenado criado para agilizar o registo de novas entregas no sistema de forma padronizada:
```
DELIMITER //

CREATE PROCEDURE sp_inserir_entrega(
    IN p_veiculo_id INT,
    IN p_motorista_id INT,
    IN p_rota_id INT,
    IN p_valor_frete DECIMAL(10,2),
    IN p_status_entrega VARCHAR(50)
)
BEGIN
    INSERT INTO entregas (veiculo_id, motorista_id, rota_id, data_saida, status_entrega, valor_frete)
    VALUES (p_veiculo_id, p_motorista_id, p_rota_id, NOW(), p_status_entrega, p_valor_frete);
END //

DELIMITER ;
```

 Como Executar
Clone este repositório ou copie os scripts SQL.

Abra o seu gestor de banco de dados MySQL (como o MySQL Workbench).

Execute o script de criação das tabelas e inserção dos dados de teste.

Explore as VIEWs e teste as chamadas das STORED PROCEDUREs.

Feito por Pedro Rafael.


