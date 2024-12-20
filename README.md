# Documentação: Criação de uma VPC na AWS usando Terraform Modular

## Estrutura do Projeto
A estrutura do projeto segue o padrão modular, permitindo reutilização, organização e escalabilidade da infraestrutura:

```
infra/
  vpc/
    main.tf
    variables.tf
modules/
  vpc/
    endpoint.tf
    internet-gateway.tf
    nacl.tf
    nat-gateway.tf
    outputs.tf
    route-tables.tf
    subnet.tf
    variables.tf
    vpc.tf
vars/
  dev/
    vpc.tfvars
```

### 1. Diretórios Principais
- **infra/vpc/**: Contém a definição principal da infraestrutura, consumindo os módulos definidos.
- **modules/vpc/**: Contém os módulos reutilizáveis que implementam recursos específicos da VPC.
- **vars/**: Contém arquivos `.tfvars` com variáveis específicas de cada ambiente (e.g., dev, prod).

---

## Configuração dos Módulos
Cada arquivo dentro do diretório `modules/vpc` implementa um recurso específico da infraestrutura. Por exemplo:

### Exemplo: **`vpc.tf`**
Define a criação da VPC principal:

```hcl
resource "aws_vpc" "main" {
  cidr_block           = var.cidr_block
  enable_dns_support   = true
  enable_dns_hostnames = true
  tags = {
    Name = var.name
  }
}
```

### Exemplo: **`variables.tf`**
Define as variáveis para o módulo:

```hcl
variable "cidr_block" {
  description = "Bloco CIDR da VPC"
  type        = string
}

variable "name" {
  description = "Nome da VPC"
  type        = string
}
```

### Exemplo: **`outputs.tf`**
Define as saídas do módulo:

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}
```

Outros arquivos no módulo seguem padrões semelhantes, configurando subnets, gateways, tabelas de rota, ACLs, endpoints, entre outros.

---

## Configuração no Diretório Principal
O diretório `infra/vpc` consome os módulos definidos para criar a infraestrutura desejada.

### Exemplo: **`main.tf`**

```hcl
module "vpc" {
  source      = "../../modules/vpc"
  cidr_block  = var.cidr_block
  name        = var.name
  # Adicione mais variáveis conforme necessário
}
```

### Exemplo: **`variables.tf`**
Define as variáveis utilizadas no diretório principal:

```hcl
variable "cidr_block" {
  description = "Bloco CIDR para a VPC"
  type        = string
  default     = "10.0.0.0/16"
}

variable "name" {
  description = "Nome da VPC"
  type        = string
  default     = "my-vpc"
}
```

---

## Configuração de Variáveis por Ambiente
Os arquivos no diretório `vars` permitem configurar variáveis específicas para diferentes ambientes, como `dev` ou `prod`.

### Exemplo: **`vars/dev/vpc.tfvars`**

```hcl
cidr_block = "10.1.0.0/16"
name       = "dev-vpc"
```

---

## Executando o Terraform

1. **Inicializar o Terraform**
   Navegue até o diretório `infra/vpc` e execute:
   ```bash
   terraform init
   ```

2. **Validar a Configuração**
   Certifique-se de que os arquivos estão configurados corretamente:
   ```bash
   terraform validate
   ```

3. **Planejar a Infraestrutura**
   Gere o plano da infraestrutura para revisão:
   ```bash
   terraform plan -var-file="../../vars/dev/vpc.tfvars"
   ```

4. **Aplicar a Configuração**
   Crie os recursos na AWS:
   ```bash
   terraform apply -var-file="../../vars/dev/vpc.tfvars"
   ```

---

## Verificação dos Recursos
Após aplicar a configuração, verifique os recursos criados no console da AWS:
- **VPC**: Certifique-se de que o CIDR configurado foi aplicado corretamente.
- **Subnets**: Verifique a separação entre subnets públicas e privadas.
- **Gateways e Tabelas de Rotas**: Teste o funcionamento dos NAT e Internet Gateways.
- **Endpoints**: Confirme que estão acessíveis conforme o esperado.

---

## Benefícios da Estrutura Modular
1. **Reutilização**: Os módulos podem ser reaproveitados em diferentes ambientes ou projetos.
2. **Organização**: Cada recurso tem um arquivo dedicado, facilitando a manutenção e o entendimento.
3. **Escalabilidade**: É fácil adicionar novos recursos sem impactar a infraestrutura existente.

---

Com essa abordagem, é possível gerenciar a infraestrutura de forma eficiente, utilizando boas práticas no Terraform para garantir modularidade e simplicidade na gestão da nuvem AWS.

