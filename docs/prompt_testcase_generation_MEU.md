Objetivo

Atue como um Analista de Qualidade (QA) Sênior, especialista em análise funcional, levantamento de requisitos, regras de negócio e elaboração de casos de teste.

Sua tarefa é realizar uma análise funcional completa do sistema McBugs, utilizando como fonte de informação o código-fonte e todos os artefatos disponíveis no projeto.

Após concluir a análise, gere um documento de casos de teste funcionais no formato Markdown (.md), pronto para ser utilizado por uma equipe de QA.

Regras Gerais
A saída final deve ser exclusivamente um documento Markdown.
O arquivo deve possuir extensão .md.
Utilize a sintaxe Markdown padrão para:
Títulos;
Subtítulos;
Listas;
Tabelas;
Ênfases;
Seções.
Não utilize HTML para estruturar o documento.
Não utilize formatos como JSON, XML ou YAML para representar os casos de teste.
Não coloque o conteúdo dentro de um bloco de código Markdown.
O documento deve ser legível tanto no GitHub quanto em editores Markdown.
Não altere o código-fonte do sistema.
Não implemente funcionalidades.
Não corrija problemas encontrados durante a análise.
O objetivo é analisar e documentar, não desenvolver.
Escopo

A análise deve considerar exclusivamente a qualidade funcional do sistema.

Fora do escopo

Não é necessário avaliar neste momento:

Performance;
Testes de carga;
Testes de estresse;
Escalabilidade;
Benchmark;
Automação de testes;
Testes de segurança avançados;
Testes de invasão.

Os testes devem ser especificados para execução manual, seguindo o formato tradicional de casos de teste.

Etapa 1 — Análise do Sistema

Antes de criar qualquer caso de teste, explore e compreenda o sistema McBugs.

Analise, quando disponíveis:

Estrutura do projeto;
Telas;
Páginas;
Componentes;
Formulários;
Campos;
Botões;
Menus;
Fluxos de navegação;
APIs;
Endpoints;
Regras de negócio;
Validações;
Mensagens de erro;
Mensagens de sucesso;
Tratamento de exceções;
Estados dos registros;
Permissões;
Perfis de usuário;
Operações de criação;
Operações de consulta;
Operações de alteração;
Operações de exclusão;
Filtros;
Ordenações;
Paginação;
Integrações;
Modelos de dados;
Banco de dados;
Configurações relevantes.

Não se limite aos nomes dos arquivos.

Sempre que possível, confirme o comportamento analisando a implementação real do sistema.

Não invente funcionalidades ou regras de negócio.

Etapa 2 — Identificação dos Cenários

Para cada funcionalidade encontrada, identifique os cenários funcionais relevantes.

Considere:

Cenários positivos
Fluxo principal;
Dados válidos;
Operações realizadas com sucesso;
Comportamento esperado após cada ação.
Cenários negativos
Campos obrigatórios não preenchidos;
Dados inválidos;
Formatos incorretos;
Valores não permitidos;
Recursos inexistentes;
Dados inconsistentes;
Operações não permitidas;
Ações realizadas em sequência incorreta.
Cenários de limite

Quando aplicável, considere:

Valores mínimos;
Valores máximos;
Valores abaixo do limite;
Valores acima do limite;
Campos vazios;
Tamanho mínimo;
Tamanho máximo;
Caracteres especiais;
Dados duplicados.
Fluxos alternativos

Considere, quando aplicável:

Cancelamento;
Voltar;
Repetição de operações;
Alteração;
Exclusão;
Recuperação após erro;
Diferentes caminhos para realizar uma mesma operação.
Regras de negócio

Para cada regra de negócio identificada, crie cenários capazes de validar:

Quando a regra deve permitir a operação;
Quando a regra deve impedir a operação;
Qual comportamento é esperado quando a regra não é atendida.
Etapa 3 — Casos de Teste

Os casos de teste devem seguir obrigatoriamente o seguinte padrão.

CT001 - Nome do Caso de Teste
Objetivo

Descrever de forma clara o objetivo do teste.

Pre Condições

Descrever as condições necessárias antes da execução.

Exemplo:

Usuário cadastrado;
Usuário autenticado;
Registro previamente existente;
Permissão necessária configurada.
Dados de Teste

Quando necessário, informar os dados utilizados durante o teste.

Campo	Valor
Usuário	usuario.teste
Senha	senha válida
Passos

Os passos devem utilizar obrigatoriamente a seguinte estrutura:

ID	Ação	Resultado Esperado
1	Acessar o sistema	Sistema é apresentado corretamente
2	Informar usuário válido	Sistema aceita o usuário informado
3	Informar senha válida	Sistema aceita a senha informada
4	Clicar em "Entrar"	Usuário é autenticado e direcionado para a tela inicial
Regras para os Casos de Teste

Cada caso de teste deve:

Possuir um objetivo funcional específico;
Ser independente sempre que possível;
Possuir pré-condições claras;
Possuir passos executáveis manualmente;
Possuir resultados esperados objetivos;
Ser compreensível por outro analista de QA;
Evitar interpretações subjetivas;
Evitar duplicidade com outros casos;
Validar uma regra ou comportamento específico.

Evite resultados esperados vagos como:

"Deve funcionar";
"Deve dar certo";
"Sistema funciona normalmente";
"Tela apresentada corretamente".

Prefira resultados específicos, por exemplo:

"O sistema deve apresentar a mensagem 'Usuário ou senha inválidos' e permanecer na tela de autenticação."

Identificação dos Casos

Os casos devem utilizar numeração sequencial:

CT001
CT002
CT003
CT004

Não pule números.

Não reutilize o mesmo identificador.

Organização do Documento Markdown

Organize os casos de teste por módulo ou funcionalidade.

Utilize a seguinte estrutura:

Casos de Teste — McBugs
1. Objetivo

Descrever o objetivo geral da documentação.

2. Escopo

Descrever o que está sendo avaliado e o que está fora do escopo.

3. Estratégia de Testes

Descrever resumidamente a abordagem utilizada para identificação dos cenários.

4. Casos de Teste
4.1 Autenticação
CT001 - Login com credenciais válidas

...

CT002 - Login com senha inválida

...

4.2 Cadastro de Usuários
CT003 - Cadastro de usuário com dados válidos

...

CT004 - Cadastro sem preenchimento de campo obrigatório

...

4.3 Consulta de Usuários
CT005 - Consulta de usuário existente

...

Cobertura Funcional

Ao final do documento, faça uma revisão da cobertura.

Crie uma seção:

5. Cobertura Funcional

Apresente uma tabela resumindo as funcionalidades analisadas e a quantidade de casos de teste criados.

ID	Funcionalidade	Cenários Positivos	Cenários Negativos	Total de CTs
F01	Autenticação	2	3	5
F02	Cadastro de Usuários	4	5	9

Os números devem ser baseados nos casos de teste realmente criados.

Não invente quantidades.

Pontos de Atenção

Caso existam funcionalidades ou regras que não possam ser determinadas com segurança a partir do projeto, crie a seção:

6. Pontos de Atenção

Utilize a seguinte estrutura:

ID	Funcionalidade	Ponto de Atenção	Impacto
PA001	Cadastro	Regra de duplicidade não identificada no código	Necessário esclarecimento

Não invente informações.

Caso não existam pontos de atenção, informe:

Não foram identificados pontos de atenção que impeçam a definição dos casos de teste funcionais.

Resumo Final

Ao final, crie a seção:

7. Resumo

Apresente um resumo contendo:

Quantidade total de funcionalidades analisadas;
Quantidade total de casos de teste;
Quantidade de cenários positivos;
Quantidade de cenários negativos;
Quantidade de cenários de limite;
Quantidade de pontos de atenção.

Os números devem ser calculados com base no conteúdo real do documento.

Processo Obrigatório

Siga esta ordem de execução:

Explorar o projeto;
Identificar a arquitetura funcional;
Identificar as funcionalidades;
Identificar as regras de negócio;
Identificar as validações;
Identificar os fluxos principais;
Identificar os fluxos alternativos;
Identificar os cenários negativos;
Identificar os cenários de limite;
Elaborar os casos de teste;
Revisar possíveis duplicidades;
Revisar a cobertura funcional;
Criar a seção de pontos de atenção;
Criar o resumo;
Gerar o documento final em Markdown (.md).

Não comece criando o CT001 antes de concluir a análise do sistema.

Critérios de Qualidade

Antes de finalizar o documento, valide:

 Todas as funcionalidades relevantes foram analisadas;
 Os principais fluxos funcionais possuem cobertura;
 Existem cenários positivos;
 Existem cenários negativos quando aplicável;
 Existem cenários de limite quando aplicável;
 As regras de negócio identificadas possuem testes;
 As validações possuem testes;
 Os casos não possuem duplicidade desnecessária;
 Os resultados esperados são objetivos;
 Os passos são executáveis manualmente;
 A numeração dos casos é sequencial;
 A cobertura funcional foi revisada;
 Os pontos de atenção foram documentados;
 O resumo possui números consistentes;
 O documento está em formato Markdown válido;
 Nenhuma informação foi inventada;
 Nenhum código-fonte foi alterado.
Entrega

Para cada casos de teste, crie um arquivo:

caso-de-teste_XX.md

dentro da pasta docs
onde xx é o numero do caso de teste

Ex:
caso-de-teste_01.md
caso-de-teste_02.md

O arquivo deve conter somente a documentação dos casos de teste em Markdown, seguindo integralmente a estrutura definida neste prompt.