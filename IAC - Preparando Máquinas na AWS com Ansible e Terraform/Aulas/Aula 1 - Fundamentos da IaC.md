Aqui está o README da Aula 1, sintetizado e organizado conforme o padrão estabelecido:

# Fundamentos da IaC

## Instalação e Configuração

- **Terraform**: Ferramenta para criar, alterar e melhorar a infraestrutura de forma previsível.
- **Ansible**: Utilizado para automatizar a instalação, atualização de aplicativos e gerenciamento de configurações.
- **AWS CLI**: Interface de linha de comando necessária para a comunicação entre as ferramentas locais e a nuvem da AWS.
- **Configuração da AWS**: Realizada através do comando `aws configure`, onde são inseridas as chaves de acesso (*Access Key ID* e *Secret Access Key*).
- **Ambiente**: Recomenda-se o uso do **WSL** (Windows Subsystem for Linux) para uma experiência nativa Unix, mas a instalação manual ou via gerenciadores de pacotes (Chocolatey/Brew) também é possível.

## Estrutura do Projeto

- **Arquivos Principais**:
  - `main.tf`: Arquivo central que contém as configurações da infraestrutura.
- **Organização**: O projeto inicia com a criação de um diretório dedicado (ex: `mkdir learn-terraform-aws-instance`) para isolar os arquivos de estado e configuração do Terraform.

## Desenvolvimento da Infraestrutura

### Especificação do Provedor

- Definição do bloco `terraform` para declarar os provedores necessários (*required_providers*).
- Uso do driver oficial da **HashiCorp** para AWS e especificação da versão mínima do Terraform.

### Configuração do Provedor

- No bloco `provider "aws"`, define-se o perfil de acesso (`profile = "default"`) e a região geográfica onde os recursos serão alocados (ex: `us-west-2`).

### Configurando a Instância (Recurso)

- Utilização do bloco `resource "aws_instance"` para definir a máquina virtual:
  - **AMI (Amazon Machine Image)**: Identificador do sistema operacional (ex: Ubuntu Server).
  - **Instance Type**: Especificação do hardware (ex: `t2.micro` para fins educacionais).
  - **Tags**: Atribuição de metadados, como o nome da instância para identificação no console AWS.

### Execução do Projeto

- **Inicialização**: `terraform init` para baixar os plugins e módulos do provedor.
- **Planejamento**: `terraform plan` para visualizar as alterações que serão feitas antes de aplicá-las (verificação de segurança).
- **Aplicação**: `terraform apply` para efetivar a criação dos recursos na nuvem.
- **Confirmação**: É necessário digitar `yes` para validar o plano de execução.

## Fundamentos e Boas Práticas

- **Idempotência**: Garantia de que a execução do código múltiplas vezes resultará sempre no mesmo estado final, sem duplicar recursos desnecessariamente.
- **Controle de Versionamento**: O uso de IaC permite que a infraestrutura seja armazenada em repositórios Git, possibilitando histórico de mudanças e *rollback* em caso de falhas.
- **Automatização vs. Manual**: Evita erros humanos comuns em processos manuais (cliques em interface gráfica) e garante agilidade no deploy.
- **Reuso de Código**: Facilidade em replicar ambientes de desenvolvimento, teste e produção através de blocos de configuração padronizados.

## Links Úteis:
- [Documentação Oficial do Terraform](https://registry.terraform.io/providers/hashicorp/aws/latest/docs)
- [Página de Download do Terraform](https://www.terraform.io/downloads)
