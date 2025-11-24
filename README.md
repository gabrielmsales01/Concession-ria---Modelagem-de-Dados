Concessionaria - Modelagem de Dados

Descricao
Banco de dados que reune informações sobre veículos, marcas, modelos, clientes, vendas e funcionários da concessionária.

Tecnologias / Ferramentas usadas
- Modelagem: draw.io
- Banco: MySQL

Links
- Diagrama online (draw.io): https://app.diagrams.net/#G1iLZHNujj4aOmP1IXcP1srnumqJC_j15k#%7B%22pageId%22%3A%22QxGqtiy630IpfRD0UHR5%22%7D  
- Arquivo (.drawio / export): https://drive.google.com/file/d/1iLZHNujj4aOmP1IXcP1srnumqJC_j15k/view?usp=sharing

O que tem neste repositório
- Diagrama DER da concessionária
- Sugestão de tabelas principais (veículos, marcas, clientes, funcionários, vendas)
Script SQL: -- Banco de dados: concessionaria_db
CREATE DATABASE IF NOT EXISTS concessionaria_db
  DEFAULT CHARACTER SET = utf8mb4
  DEFAULT COLLATE = utf8mb4_unicode_ci;
USE concessionaria_db;

-- Marcas
CREATE TABLE IF NOT EXISTS marca (
  marca_id BIGINT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(150) NOT NULL
) ENGINE=InnoDB;

-- Modelos (relacionados a uma marca)
CREATE TABLE IF NOT EXISTS modelo (
  modelo_id BIGINT AUTO_INCREMENT PRIMARY KEY,
  marca_id BIGINT NOT NULL,
  nome VARCHAR(200) NOT NULL,
  ano_min SMALLINT,
  ano_max SMALLINT,
  FOREIGN KEY (marca_id) REFERENCES marca(marca_id) ON DELETE CASCADE
) ENGINE=InnoDB;

-- Veículos (itens no estoque; cada veículo único tem um código/placa/VIN)
CREATE TABLE IF NOT EXISTS veiculo (
  veiculo_id BIGINT AUTO_INCREMENT PRIMARY KEY,
  modelo_id BIGINT NOT NULL,
  vin VARCHAR(50) UNIQUE,
  placa VARCHAR(15),
  cor VARCHAR(50),
  quilometragem INT DEFAULT 0,
  preco DECIMAL(10,2),
  status ENUM('em_estoque','vendido','reservado') DEFAULT 'em_estoque',
  FOREIGN KEY (modelo_id) REFERENCES modelo(modelo_id) ON DELETE CASCADE
) ENGINE=InnoDB;

-- Clientes
CREATE TABLE IF NOT EXISTS cliente (
  cliente_id BIGINT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(200) NOT NULL,
  cpf VARCHAR(20),
  email VARCHAR(200),
  telefone VARCHAR(30)
) ENGINE=InnoDB;

-- Funcionários
CREATE TABLE IF NOT EXISTS funcionario (
  funcionario_id BIGINT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(200) NOT NULL,
  cargo VARCHAR(100),
  email VARCHAR(200)
) ENGINE=InnoDB;

-- Vendas
CREATE TABLE IF NOT EXISTS venda (
  venda_id BIGINT AUTO_INCREMENT PRIMARY KEY,
  veiculo_id BIGINT NOT NULL,
  cliente_id BIGINT NOT NULL,
  funcionario_id BIGINT,
  data_venda DATE DEFAULT CURDATE(),
  valor_final DECIMAL(12,2),
  forma_pagamento VARCHAR(100),
  FOREIGN KEY (veiculo_id) REFERENCES veiculo(veiculo_id) ON DELETE RESTRICT,
  FOREIGN KEY (cliente_id) REFERENCES cliente(cliente_id) ON DELETE SET NULL,
  FOREIGN KEY (funcionario_id) REFERENCES funcionario(funcionario_id) ON DELETE SET NULL
) ENGINE=InnoDB;

-- Índices
CREATE INDEX idx_cliente_nome ON cliente(nome);
CREATE INDEX idx_veiculo_status ON veiculo(status);

-- Inserts de exemplo: marcas, modelos, veículos, clientes, funcionários, venda
INSERT INTO marca (nome) VALUES ('Toyota'), ('Volkswagen'), ('Chevrolet');

INSERT INTO modelo (marca_id, nome, ano_min, ano_max) VALUES
  (1, 'Corolla', 2005, 2025),
  (2, 'Gol', 1998, 2022),
  (3, 'Onix', 2012, 2025);

INSERT INTO veiculo (modelo_id, vin, placa, cor, quilometragem, preco, status) VALUES
  (1, 'VIN000001', 'ABC1D23', 'Prata', 45000, 85000.00, 'em_estoque'),
  (2, 'VIN000002', 'XYZ2A45', 'Vermelho', 120000, 30000.00, 'em_estoque'),
  (3, 'VIN000003', 'JKL3B67', 'Preto', 25000, 60000.00, 'em_estoque');

INSERT INTO cliente (nome, cpf, email, telefone) VALUES
  ('Rafael Mendes', '000.000.000-00', 'rafael@example.com', '97777-0000'),
  ('Sofia Andrade', '111.111.111-11', 'sofia@example.com', '96666-0000');

INSERT INTO funcionario (nome, cargo, email) VALUES
  ('Pedro Varela', 'Vendedor', 'pedro@concessionaria.com'),
  ('Luciana Dias', 'Gerente', 'luciana@concessionaria.com');

-- Exemplo de venda: vender o veiculo_id = 1 para cliente 1, por funcionario 1
INSERT INTO venda (veiculo_id, cliente_id, funcionario_id, data_venda, valor_final, forma_pagamento) VALUES
  (1, 1, 1, CURDATE(), 82000.00, 'Financiamento');

-- Atualiza status do veículo para vendido
UPDATE veiculo SET status='vendido' WHERE veiculo_id=1;
