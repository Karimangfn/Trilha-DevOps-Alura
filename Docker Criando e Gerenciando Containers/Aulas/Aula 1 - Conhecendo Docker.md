# Gerenciamento e Vantagens do Uso de Containers

## Desafios ao Gerenciar Múltiplas Aplicações
Discutimos os desafios comuns ao gerenciar múltiplas aplicações que precisam rodar simultaneamente, como Nginx, Java e C#. Um dos principais problemas é o conflito de portas, já que todas as aplicações precisam da porta 80. Também exploramos as dificuldades relacionadas ao controle de recursos como memória e CPU, além da manutenção das aplicações.

## Máquinas Virtuais vs. Containers
Uma solução inicial seria a utilização de máquinas físicas separadas para cada aplicação, mas isso é inviável financeiramente. Em seguida, exploramos as máquinas virtuais, que fornecem isolamento entre as aplicações, mas questionamos se a virtualização completa de um sistema operacional é realmente necessária. Observamos que containers oferecem uma alternativa mais eficiente e econômica, permitindo o isolamento sem a necessidade de virtualizar um sistema operacional inteiro.

## Funcionamento dos Containers
Discutimos como os containers funcionam de forma mais leve e eficiente em comparação com as máquinas virtuais. Ao contrário das máquinas virtuais, que virtualizam um sistema operacional completo, os containers operam diretamente no sistema operacional, isolando processos em ambientes leves e consumindo menos recursos. 

## Isolamento e Namespaces
Os containers garantem isolamento por meio dos **namespaces**, que criam espaços separados para diferentes aspectos do sistema, como processos, rede, intercomunicação, sistema de arquivos e acesso ao kernel. Isso permite que cada container tenha seu próprio ambiente isolado, sem interferir em outros containers.

## Gerenciamento de Recursos com Cgroups
Exploramos o uso dos **Cgroups**, que permitem limitar e gerenciar o consumo de recursos, como memória e CPU, de cada container. Com isso, conseguimos garantir um controle eficiente sobre os recursos usados por cada aplicação, evitando que um container consuma recursos excessivos e impacte outros containers ou o sistema como um todo.