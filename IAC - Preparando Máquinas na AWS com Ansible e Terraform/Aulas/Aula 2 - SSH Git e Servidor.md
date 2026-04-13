# SSH, Git e Servidor

## Instalação e Configuração

- **Par de Chaves (Key Pair)**: Essencial para o acesso seguro via SSH às instâncias EC2. 
  - Criado no console AWS em *Rede e Segurança > Pares de Chaves*.
  - Formato recomendado: `.pem` (padrão para OpenSSH).
- **Permissões de Arquivo**: No Linux/Unix, é obrigatório restringir as permissões da chave privada para que o SSH funcione:
  - Comando: `chmod 400 iac-alura.pem` (concede leitura apenas ao proprietário).
- **Acesso SSH**: Realizado via terminal com o comando:
  `ssh -i "chave.pem" ubuntu@ip-publico-da-instancia`.

## Estrutura do Projeto

- **Arquivos Adicionados**:
  - `iac-alura.pem`: Chave privada para acesso ao servidor (deve ser mantida em local seguro).
  - `.gitignore`: Configurado para impedir o versionamento de arquivos sensíveis ou temporários.
- **Modificações no `main.tf`**:
  - Inclusão do argumento `key_name = "iac-alura"` dentro do recurso `aws_instance` para associar a chave à máquina no momento da criação.

## Desenvolvimento da Infraestrutura

### Ciclo de Vida do Recurso (Replace)

- Ao adicionar uma chave SSH (`key_name`) a uma instância já existente, o Terraform detecta que a alteração requer a **substituição** do recurso.
- O Terraform destrói a instância antiga e cria uma nova com a configuração atualizada.

### Configuração de Segurança (Security Groups)

- **Inbound Rules (Entrada)**: Devem ser configuradas no console AWS para permitir o tráfego.
  - Para testes de desenvolvimento, permitiu-se "Todo o tráfego" para `0.0.0.0/0` (IPv4) e `::/0` (IPv6).
- **Outbound Rules (Saída)**: Configuradas para permitir que a instância envie dados para a internet.

### Servidor Web Temporário

- Criação de um arquivo `index.html` básico: `echo "<h1>Ola mundo</h1>" > index.html`.
- Uso do **BusyBox** para subir um servidor HTTP rápido na porta 8080:
  - Comando: `nohup busybox httpd -f -p 8080 &`
  - O `nohup` e o `&` garantem que o servidor continue rodando após o fechamento da sessão SSH.

## Boas Práticas de Infraestrutura

- **Versionamento Seguro**: Uso do `.gitignore` para excluir arquivos de estado do Terraform (`.tfstate`), diretórios temporários (`.terraform/`) e chaves privadas (`*.pem`).
- **Idempotência**: O Terraform gerencia o estado da infraestrutura, garantindo que apenas as mudanças necessárias sejam aplicadas.
- **Segurança Restritiva**: O uso de `chmod 400` protege a chave contra acessos não autorizados por outros usuários do sistema local.

## Links Úteis:
- [Gitignore.io (Templates para Terraform)](https://www.toptal.com/developers/gitignore/api/terraform)
- [Documentação AWS sobre Grupos de Segurança](https://docs.aws.amazon.com/pt_br/vpc/latest/userguide/VPC_SecurityGroups.html)
