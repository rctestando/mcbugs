# Casos de Teste — McBugs

## 1. Objetivo

Documentar os casos de teste funcionais manuais do **McBugs**, totem de autoatendimento para pedidos em restaurante, de forma que qualquer analista de QA ou desenvolvedor consiga executar os fluxos, verificar os resultados e decidir se o comportamento está de acordo com a implementação atual.

## 2. Escopo

**Em escopo:** fluxos funcionais da interface do totem, regras de negócio identificadas no código-fonte, validações de formulário, estados de tela, persistência local do pedido e criação/atualização de pedido no backend (Supabase).

**Fora de escopo:** performance, carga, estresse, escalabilidade, automação, testes de segurança avançados e testes de invasão.

**Fonte da análise:** código-fonte do projeto (páginas, componentes, `CartContext`, dados estáticos de produtos, rotas e schema da tabela `orders`). Nenhuma funcionalidade foi inventada.

## 3. Estratégia de Testes

Os cenários foram derivados da navegação real:

1. Tela inicial (`/`) — escolha de tipo de pedido (`dine-in` ou `takeaway`).
2. Cardápio (`/menu`) — categorias e listagem de produtos.
3. Detalhe do produto (`/product/:id`) — quantidade e inclusão no carrinho.
4. Carrinho (`/cart`) — alteração, exclusão, total e checkout com nome do cliente.
5. Pagamento (`/payment`) — PIX, débito ou crédito; cancelamento.
6. Confirmação (`/payment/:method/confirm`) — resumo e novo pedido.
7. Rota inexistente — página 404.

Foram cobertos cenários positivos, negativos, de limite e fluxos alternativos (voltar, cancelar, continuar comprando, recarregar a página).

**Pré-condição geral (quando não indicado o contrário):** aplicação em execução (ex.: `yarn dev` em `http://localhost:8080`), backend Supabase configurado e acessível para os casos que criam ou atualizam pedido.

---

## 4. Casos de Teste

### 4.1 Tela inicial e tipo de pedido

### **CT001 - Exibir tela inicial do totem**

#### **Objetivo**

Validar que a rota `/` apresenta a identidade McBugs, a mensagem de boas-vindas e as duas formas de consumo.

#### **Pré-Condições**

- O sistema está online e acessível.
- Acessar a aplicação pela URL raiz.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Acessar `/` | A página inicial é carregada. |
| 2 | Observar título e subtítulo | São exibidos **McBugs** e **Seja bem-vindo!**. |
| 3 | Observar as opções de consumo | Existem os botões **Para comer aqui** e **Para levar**, cada um com imagem. |

#### **Resultados Esperados**

- A tela inicial é exibida sem autenticação (totem público).
- As duas opções de tipo de pedido estão visíveis e clicáveis.

#### **Critérios de Aceitação**

- Textos **McBugs** e **Seja bem-vindo!** estão visíveis.
- Os botões **Para comer aqui** e **Para levar** estão visíveis.

---

### **CT002 - Selecionar pedido para comer no local**

#### **Objetivo**

Validar que **Para comer aqui** grava o tipo `dine-in` e redireciona para o cardápio.

#### **Pré-Condições**

- Usuário está na tela inicial (`/`).

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar em **Para comer aqui** | O sistema navega para `/menu`. |
| 2 | Verificar a URL e o cardápio | A URL é `/menu` e o cardápio é apresentado. |

#### **Resultados Esperados**

- O tipo de pedido fica `dine-in` (persistido em `localStorage` na chave `mcbugs-order-type`).
- O usuário permanece no fluxo do cardápio.

#### **Critérios de Aceitação**

- Após o clique, a URL é `/menu`.
- O valor `dine-in` é armazenado em `localStorage` (`mcbugs-order-type`).

---

### **CT003 - Selecionar pedido para levar**

#### **Objetivo**

Validar que **Para levar** grava o tipo `takeaway` e redireciona para o cardápio.

#### **Pré-Condições**

- Usuário está na tela inicial (`/`).

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar em **Para levar** | O sistema navega para `/menu`. |
| 2 | Verificar persistência do tipo | O valor `takeaway` está em `localStorage` (`mcbugs-order-type`). |

#### **Resultados Esperados**

- O cardápio é aberto com o tipo de pedido **para levar** definido.

#### **Critérios de Aceitação**

- URL `/menu` após o clique.
- `mcbugs-order-type` igual a `takeaway`.

---

### 4.2 Cardápio

### **CT004 - Exibir cardápio com categoria Lanches padrão**

#### **Objetivo**

Validar que o cardápio inicia na categoria **Lanches** e lista os lanches cadastrados.

#### **Pré-Condições**

- Tipo de pedido já selecionado.
- Usuário em `/menu`.

#### **Dados de Teste**

| Campo | Valor |
|-------|--------|
| Categoria inicial | lanches |
| Produtos esperados | Big Mock, Duplo Deploy, McMerge, McNífico Flaky |

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Observar as abas de categoria | As abas **Lanches**, **Fritas**, **Bebidas** e **Sobremesas** estão visíveis. |
| 2 | Observar a aba ativa | **Lanches** está destacada como selecionada. |
| 3 | Observar a grade de produtos | São exibidos os 4 lanches com nome, descrição e preço em Real. |

#### **Resultados Esperados**

- Categoria padrão é `lanches`.
- Preços no formato brasileiro (ex.: **R$ 39,90**).

#### **Critérios de Aceitação**

- Aba **Lanches** ativa ao entrar no cardápio.
- Os quatro lanches do catálogo estático são listados.

---

### **CT005 - Filtrar produtos por categoria**

#### **Objetivo**

Validar que a troca de aba filtra apenas os produtos da categoria escolhida.

#### **Pré-Condições**

- Usuário em `/menu`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar em **Fritas** | São listados Batatas Full Stack, Batatas Refatoradas e Batatas Minificadas. |
| 2 | Clicar em **Bebidas** | São listados Coca-Crash, Fanta Warning e Água Localhost. |
| 3 | Clicar em **Sobremesas** | São listados Casquinha Vanilla JS, Casquinha Dark Mode e Casquinha Pull Request. |
| 4 | Clicar em **Lanches** | A lista volta aos 4 lanches. |

#### **Resultados Esperados**

- Cada aba exibe somente produtos daquela categoria.
- A aba clicada permanece visualmente selecionada.

#### **Critérios de Aceitação**

- Nenhum produto de outra categoria aparece na grade após o filtro.
- Quantidade de itens: 3 fritas, 3 bebidas, 3 sobremesas, 4 lanches.

---

### **CT006 - Voltar do cardápio para a tela inicial**

#### **Objetivo**

Validar o botão de voltar (seta) no hero do cardápio.

#### **Pré-Condições**

- Usuário em `/menu`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar no botão de seta no canto superior esquerdo da imagem | O sistema navega para `/`. |
| 2 | Observar a tela | A tela inicial com as opções de consumo é exibida. |

#### **Resultados Esperados**

- Retorno à escolha de tipo de pedido.

#### **Critérios de Aceitação**

- URL igual a `/` após o clique.

---

### **CT007 - Exibir status Aberto e identidade no cardápio**

#### **Objetivo**

Validar os elementos informativos do painel do restaurante no cardápio.

#### **Pré-Condições**

- Usuário em `/menu`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Observar o painel abaixo da imagem | Logo McBugs, texto **O fast food favorito da comunidade de QAs** e indicador **Aberto!** estão visíveis. |

#### **Resultados Esperados**

- O status **Aberto!** é apresentado (valor fixo na interface; não há regra de horário no código).

#### **Critérios de Aceitação**

- Texto **Aberto!** visível no cardápio.

---

### **CT008 - Abrir detalhe ao clicar no card do produto**

#### **Objetivo**

Validar a navegação do cardápio para `/product/:id`.

#### **Pré-Condições**

- Usuário em `/menu`, categoria **Lanches**.

#### **Dados de Teste**

| Campo | Valor |
|-------|--------|
| Produto | Big Mock |
| ID | big-mock |

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar no card **Big Mock** | O sistema navega para `/product/big-mock`. |
| 2 | Observar a tela | Nome **Big Mock**, preço **R$ 39,90**, descrição e lista de ingredientes são exibidos. |

#### **Resultados Esperados**

- Detalhe corresponde ao produto clicado.

#### **Critérios de Aceitação**

- URL `/product/big-mock`.
- Preço unitário **R$ 39,90**.

---

### 4.3 Detalhe do produto

### **CT009 - Produto inexistente**

#### **Objetivo**

Validar o tratamento quando o `id` da URL não existe no catálogo.

#### **Pré-Condições**

- Aplicação online.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Acessar `/product/produto-invalido` | A tela exibe o texto **Produto não encontrado**. |

#### **Resultados Esperados**

- Não é apresentada a tela de detalhe com botão de compra.

#### **Critérios de Aceitação**

- Mensagem **Produto não encontrado** visível.
- Botão **Quero** não é apresentado.

---

### **CT010 - Quantidade mínima 1 no detalhe do produto**

#### **Objetivo**

Validar que a quantidade inicial é 1 e o botão de diminuir fica desabilitado nesse valor.

#### **Pré-Condições**

- Usuário na tela de detalhe de um produto existente (ex.: `/product/coca-crash`).

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Observar o controle de quantidade | O valor exibido é **1**. |
| 2 | Observar o botão de diminuir (−) | O botão está desabilitado. |
| 3 | Clicar no botão de aumentar (+) | A quantidade passa a **2**. |
| 4 | Clicar em (−) | A quantidade volta a **1** e o botão (−) volta a ficar desabilitado. |

#### **Resultados Esperados**

- A quantidade no detalhe nunca fica abaixo de 1.
- O valor do botão **Quero** é `preço × quantidade`.

#### **Critérios de Aceitação**

- Quantidade mínima = 1.
- Com quantidade 2 no Coca-Crash (R$ 5,90), o botão exibe **Quero • R$ 11,80**.

---

### **CT011 - Adicionar produto ao carrinho e retornar ao cardápio**

#### **Objetivo**

Validar que **Quero** adiciona o item com a quantidade escolhida e navega para `/menu`.

#### **Pré-Condições**

- Carrinho vazio.
- Usuário em `/product/agua-localhost`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Aumentar quantidade para 3 | Controle exibe **3**. |
| 2 | Clicar em **Quero** | O sistema navega para `/menu`. |
| 3 | Observar a barra inferior do pedido | A barra **CartBar** aparece com total **R$ 8,70** e **3 itens**. |

#### **Resultados Esperados**

- Item **Água Localhost** entra no carrinho com quantidade 3.
- A barra inferior só aparece quando há itens.

#### **Critérios de Aceitação**

- Após adicionar, URL `/menu` e CartBar visível com 3 itens e total **R$ 8,70**.

---

### **CT012 - Voltar do detalhe sem adicionar ao carrinho**

#### **Objetivo**

Validar que a seta de voltar retorna ao cardápio sem alterar o carrinho.

#### **Pré-Condições**

- Carrinho vazio.
- Usuário em `/product/mcmerge`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar na seta no canto superior esquerdo | Navegação para `/menu`. |
| 2 | Observar a parte inferior da tela | A CartBar **não** é exibida. |

#### **Resultados Esperados**

- Carrinho permanece vazio.

#### **Critérios de Aceitação**

- URL `/menu` e ausência da barra **Ver pedido**.

---

### **CT013 - Atalho para o carrinho no detalhe quando há itens**

#### **Objetivo**

Validar o ícone de sacola no detalhe do produto, exibido somente se o carrinho tiver itens.

#### **Pré-Condições**

- Há pelo menos 1 item no carrinho.
- Usuário em qualquer `/product/:id` válido.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Observar o canto superior direito da imagem | Ícone de sacola com badge numérico igual à soma das quantidades. |
| 2 | Clicar no ícone | Navegação para `/cart`. |

#### **Resultados Esperados**

- Com carrinho vazio, o ícone **não** aparece (comportamento do código).

#### **Critérios de Aceitação**

- Badge igual a `getItemCount()` (soma das quantidades).
- Clique abre **Meu pedido**.

---

### 4.4 Carrinho e barra de pedido

### **CT014 - CartBar oculta com carrinho vazio**

#### **Objetivo**

Validar que a barra inferior do cardápio não aparece sem itens.

#### **Pré-Condições**

- Carrinho vazio (`localStorage` sem `mcbugs-cart-items` ou array vazio).
- Usuário em `/menu`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Observar a parte inferior da tela | Não há barra com **Ver pedido**. |

#### **Resultados Esperados**

- `CartBar` retorna `null` quando `items.length === 0`.

#### **Critérios de Aceitação**

- Botão **Ver pedido** ausente.

---

### **CT015 - Abrir Meu pedido pela CartBar**

#### **Objetivo**

Validar o botão **Ver pedido** e o texto de total/quantidade na barra.

#### **Pré-Condições**

- Carrinho com 1 Big Mock (quantidade 1).
- Usuário em `/menu`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Observar a CartBar | Texto **Total dos pedidos**, valor **R$ 39,90** e **/ 1 item**. |
| 2 | Clicar em **Ver pedido** | Navegação para `/cart` com título **Meu pedido**. |

#### **Resultados Esperados**

- Plural **itens** quando a quantidade total for maior que 1.

#### **Critérios de Aceitação**

- Com 1 unidade: rótulo **item**.
- Com 2 ou mais: rótulo **itens**.

---

### **CT016 - Carrinho vazio**

#### **Objetivo**

Validar o estado vazio da tela `/cart`.

#### **Pré-Condições**

- Carrinho sem itens.
- Acessar `/cart` diretamente ou após remover todos os itens.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Observar a tela | Título **Nada Encontrado Aqui** e texto **Seu carrinho está vazio. Adicione alguns itens deliciosos!**. |
| 2 | Clicar em **Ver cardápio** | Navegação para `/menu`. |
| 3 | Clicar na seta de voltar (se ainda estiver no vazio) | Também navega para `/menu`. |

#### **Resultados Esperados**

- Não há botão **Finalizar pedido** nesse estado.

#### **Critérios de Aceitação**

- Mensagens de carrinho vazio visíveis.
- **Ver cardápio** leva a `/menu`.

---

### **CT017 - Alterar quantidade de item no carrinho**

#### **Objetivo**

Validar incremento e decremento na tela **Meu pedido**, com mínimo 1 no controle visual.

#### **Pré-Condições**

- Carrinho com 1 Casquinha Dark Mode (quantidade 1).
- Usuário em `/cart`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar em (+) no item | Quantidade 2; subtotal **R$ 7,80**; total do pedido **R$ 7,80**. |
| 2 | Clicar em (−) | Quantidade 1; subtotal **R$ 3,90**. |
| 3 | Observar o botão (−) com quantidade 1 | Botão desabilitado; o item **não** é removido pelo (−). |

#### **Resultados Esperados**

- A remoção do item não ocorre pelo (−) na UI (mínimo 1); a exclusão é pelo ícone de lixeira.
- Se a quantidade fosse programaticamente ≤ 0, o item seria removido (`updateQuantity`).

#### **Critérios de Aceitação**

- Quantidade no carrinho não desce abaixo de 1 pela interface.
- Subtotal = preço unitário × quantidade.

---

### **CT018 - Remover item do carrinho**

#### **Objetivo**

Validar exclusão pelo ícone de lixeira.

#### **Pré-Condições**

- Carrinho com dois produtos distintos (ex.: McNífico Flaky e Coca-Crash).
- Usuário em `/cart`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar na lixeira do McNífico Flaky | O item desaparece da lista. |
| 2 | Observar o total | Total igual apenas ao valor da Coca-Crash (**R$ 5,90** se quantidade 1). |
| 3 | Clicar na lixeira do item restante | A tela passa ao estado **Nada Encontrado Aqui**. |

#### **Resultados Esperados**

- Remover o último item exibe o estado vazio.

#### **Critérios de Aceitação**

- Item removido não permanece na lista.
- Total recalculado após cada exclusão.

---

### **CT019 - Acumular quantidade ao adicionar o mesmo produto**

#### **Objetivo**

Validar que incluir novamente um produto já no carrinho soma a quantidade, sem duplicar linha.

#### **Pré-Condições**

- Carrinho vazio.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Adicionar Big Mock com quantidade 1 | CartBar indica 1 item. |
| 2 | Abrir novamente Big Mock e adicionar quantidade 2 | Uma única linha **Big Mock** com quantidade **3**. |
| 3 | Abrir `/cart` | Uma linha; total **R$ 119,70**. |

#### **Resultados Esperados**

- Não há duas linhas do mesmo `product.id`.

#### **Critérios de Aceitação**

- Quantidade consolidada = 3.
- Total = 3 × 39,90 = **R$ 119,70**.

---

### **CT020 - Continuar comprando**

#### **Objetivo**

Validar o botão **Continuar comprando** no carrinho com itens.

#### **Pré-Condições**

- Carrinho com pelo menos 1 item.
- Usuário em `/cart`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar em **Continuar comprando** | Navegação para `/menu` mantendo os itens. |
| 2 | Observar a CartBar | Os itens anteriores continuam no pedido. |

#### **Resultados Esperados**

- Carrinho não é limpo.

#### **Critérios de Aceitação**

- URL `/menu` e itens preservados.

---

### 4.5 Checkout (nome do cliente)

### **CT021 - Abrir e cancelar o drawer de finalização**

#### **Objetivo**

Validar abertura do drawer **Finalizar Pedido** e o cancelamento sem criar pedido.

#### **Pré-Condições**

- Carrinho com pelo menos 1 item.
- Usuário em `/cart`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar em **Finalizar pedido** | Abre o drawer com título **Finalizar Pedido**, campo **Seu nome** e botões **Cancelar** e **Finalizar**. |
| 2 | Clicar em **Cancelar** | O drawer fecha; o usuário permanece em `/cart` com os mesmos itens. |

#### **Resultados Esperados**

- Nenhum pedido é criado no Supabase.
- Carrinho permanece inalterado.

#### **Critérios de Aceitação**

- Drawer fecha ao cancelar.
- URL continua `/cart`.

---

### **CT022 - Nome pré-preenchido na primeira abertura do drawer**

#### **Objetivo**

Validar o valor inicial do campo nome na primeira montagem do componente.

#### **Pré-Condições**

- Carrinho com itens.
- Página `/cart` recém-carregada (drawer ainda não foi aberto e fechado nesta sessão do componente).

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar em **Finalizar pedido** | O campo **Seu nome** está preenchido com **Fernando**. |
| 2 | Fechar o drawer (Cancelar) e abrir novamente | O campo é limpo (efeito ao fechar define nome como string vazia). |

#### **Resultados Esperados**

- Estado inicial do campo é `'Fernando'`.
- Ao fechar o drawer, o nome é resetado para vazio.

#### **Critérios de Aceitação**

- Primeira abertura: valor **Fernando**.
- Reabertura após fechar: campo vazio (placeholder **Digite seu nome**).

---

### **CT023 - Bloquear finalização com nome vazio ou somente espaços**

#### **Objetivo**

Validar que o pedido não é enviado sem nome válido (`trim`).

#### **Pré-Condições**

- Carrinho com itens.
- Drawer aberto com campo nome vazio (após fechar e reabrir, ou apagando o texto).

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Deixar o campo **Seu nome** vazio e clicar em **Finalizar** | O envio não ocorre (atributo `required` do input e/ou `if (!name.trim()) return`). |
| 2 | Informar apenas espaços e tentar finalizar | O envio não ocorre (`!name.trim()`). |
| 3 | Observar a URL | Permanece em `/cart`. |

#### **Resultados Esperados**

- Pedido não é criado.
- Carrinho não é esvaziado.

#### **Critérios de Aceitação**

- Sem navegação para `/payment`.
- Itens do carrinho intactos.

---

### **CT024 - Finalizar pedido com nome válido**

#### **Objetivo**

Validar criação do pedido, loading do drawer e redirecionamento para pagamento.

#### **Pré-Condições**

- Carrinho com itens.
- Tipo de pedido definido.
- Supabase acessível.

#### **Dados de Teste**

| Campo | Valor |
|-------|--------|
| Nome | Maria QA |
| Método gravado na criação | pix (temporário, definido no código) |
| Status inicial | pending |

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Abrir **Finalizar pedido** e informar **Maria QA** | O campo aceita o texto. |
| 2 | Clicar em **Finalizar** | É exibido o estado de loading com o texto **enviando a cozinha....** e **Aguarde enquanto preparamos o seu pagamento.** |
| 3 | Aguardar o retorno da API | Navegação para `/payment`. |
| 4 | Observar o resumo | Número do pedido (`#` + id) e total igual ao total do carrinho no momento da criação. |

#### **Resultados Esperados**

- Registro inserido em `orders` com `customer_name`, `order_type`, `payment_method = pix`, `status = pending`, `total` e `items`.
- Itens do carrinho são limpos após sucesso (`setItems([])`).
- Se a API falhar, o drawer permanece aberto (loading é desligado no `catch`) e não há redirecionamento.

#### **Critérios de Aceitação**

- Sucesso: URL `/payment`, número de pedido visível, total correto.
- Carrinho vazio após sucesso (CartBar ausente se voltar ao menu).

---

### **CT025 - Falha ao criar pedido no backend**

#### **Objetivo**

Validar o fluxo negativo quando o insert no Supabase falha.

#### **Pré-Condições**

- Carrinho com itens.
- Backend indisponível ou credenciais inválidas (ex.: `.env.local` incorreto), de forma que `createOrder` lance erro.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Informar um nome válido e clicar em **Finalizar** | O loading pode aparecer. |
| 2 | Aguardar a falha | O usuário permanece no contexto do carrinho/drawer; não navega para `/payment`. |
| 3 | Verificar o carrinho | Os itens **não** foram removidos (limpeza só ocorre após insert com sucesso). |

#### **Resultados Esperados**

- Erro é registrado no console (`Error creating order`).
- Comentário no código menciona toast, mas **não há toast implementado** em `CartContext`.

#### **Critérios de Aceitação**

- Sem tela de pagamento.
- Itens ainda no carrinho.

---

### 4.6 Pagamento

### **CT026 - Exibir resumo e formas de pagamento**

#### **Objetivo**

Validar a tela `/payment` após pedido criado.

#### **Pré-Condições**

- Pedido vigente em memória (`currentOrder`).
- Usuário em `/payment`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Observar o card de resumo | Logo, **Pedido** com `#{id}` e **Total do pedido** formatado em BRL. |
| 2 | Observar as opções | Três opções: **PIX**, **Cartão de Débito** e **Cartão de Crédito**, todas com a descrição **Pagamento no balcão na hora da retirada**. |
| 3 | Observar a ação inferior | Botão **Cancelar Pedido** visível. |

#### **Resultados Esperados**

- Título **Escolha a forma de pagamento**.

#### **Critérios de Aceitação**

- As três formas estão clicáveis.
- Total igual a `currentOrder.total`.

---

### **CT027 - Selecionar forma de pagamento PIX**

#### **Objetivo**

Validar atualização do método para `pix` e navegação para a confirmação.

#### **Pré-Condições**

- Pedido vigente em `/payment`.
- Supabase acessível para o `update`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar em **PIX** | O sistema atualiza `payment_method` no pedido. |
| 2 | Observar a navegação | URL `/payment/pix/confirm`. |
| 3 | Observar o cabeçalho | Ícone/título **PIX**. |

#### **Resultados Esperados**

- Em caso de erro no update, a navegação **não** ocorre (`catch` apenas registra no console).

#### **Critérios de Aceitação**

- Sucesso: URL `/payment/pix/confirm`.
- Detalhes exibem forma de pagamento **PIX**.

---

### **CT028 - Selecionar cartão de débito**

#### **Objetivo**

Validar o fluxo de débito até a confirmação.

#### **Pré-Condições**

- Pedido vigente em `/payment`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar em **Cartão de Débito** | Navegação para `/payment/debit/confirm`. |
| 2 | Observar o título | **Cartão de Débito**. |
| 3 | Observar a instrução | **Pagamento no Balcão** e texto para pagar com **Cartão de Débito**. |

#### **Resultados Esperados**

- Método persistido como `debit`.

#### **Critérios de Aceitação**

- URL `/payment/debit/confirm`.
- Campo **Forma de pagamento** = **Cartão de Débito**.

---

### **CT029 - Selecionar cartão de crédito**

#### **Objetivo**

Validar o fluxo de crédito até a confirmação.

#### **Pré-Condições**

- Pedido vigente em `/payment`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar em **Cartão de Crédito** | Navegação para `/payment/credit/confirm`. |
| 2 | Observar o título e a instrução | **Cartão de Crédito** e pagamento no balcão. |

#### **Resultados Esperados**

- Método persistido como `credit`.
- A tela usada pela rota é `PaymentConfirm`, não o arquivo órfão `PaymentCard.tsx`.

#### **Critérios de Aceitação**

- URL `/payment/credit/confirm`.
- **Forma de pagamento** = **Cartão de Crédito**.

---

### **CT030 - Cancelar pedido na tela de pagamento**

#### **Objetivo**

Validar **Cancelar Pedido**: status `cancelled` no backend (quando possível) e limpeza total do estado local.

#### **Pré-Condições**

- Pedido vigente em `/payment`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar em **Cancelar Pedido** | O sistema tenta atualizar o status para `cancelled`. |
| 2 | Observar a navegação | Redirecionamento para `/`. |
| 3 | Verificar `localStorage` | Removidas as chaves `mcbugs-cart-items`, `mcbugs-order-type` e `mcbugs-current-order`. |
| 4 | Tentar voltar a `/payment` | Redirecionamento para `/` por ausência de `currentOrder`. |

#### **Resultados Esperados**

- A limpeza local ocorre **mesmo se** a atualização no banco falhar.

#### **Critérios de Aceitação**

- Usuário na tela inicial.
- Sem pedido vigente na sessão.

---

### **CT031 - Acessar /payment sem pedido vigente**

#### **Objetivo**

Validar proteção de rota da tela de pagamento.

#### **Pré-Condições**

- Sem `currentOrder` (sessão nova ou após reset).

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Acessar `/payment` diretamente | O sistema redireciona para `/`. |

#### **Resultados Esperados**

- Formulário de pagamento não é renderizado (`return null` após `navigate`).

#### **Critérios de Aceitação**

- URL final `/`.

---

### 4.7 Confirmação do pedido

### **CT032 - Exibir detalhes completos na confirmação**

#### **Objetivo**

Validar o conteúdo da tela `/payment/:method/confirm`.

#### **Pré-Condições**

- Pedido criado com nome **Maria QA**, tipo **Para comer aqui**, itens conhecidos.
- Usuário em `/payment/pix/confirm` (ou outro método válido).

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Observar resumo | Pedido `#{id}` e total em BRL. |
| 2 | Observar **Detalhes do pedido** | **Cliente** = Maria QA; **Tipo** = **Comer no local**; **Forma de pagamento** conforme a URL; **Data** no formato `dd/mm/aaaa hh:mm`. |
| 3 | Observar **Itens** | Cada linha no formato `{quantidade}x {nome}` e subtotal. |
| 4 | Observar o aviso de local | Faixa com **Após o pagamento, aguarde ser chamado pelo número do seu pedido.** (apenas `dine-in`). |

#### **Resultados Esperados**

- Tipo `takeaway` exibe **Para levar** e **não** exibe o aviso com ícone de mapa.
- O status do pedido **não** é alterado para `paid` nesta tela (`completePayment` não é chamado em `PaymentConfirm`).

#### **Critérios de Aceitação**

- Dados do cliente, tipo, método, data e itens conferem com o pedido criado.
- Aviso de chamada no balcão visível somente para **Comer no local**.

---

### **CT033 - Confirmação de pedido para levar**

#### **Objetivo**

Validar o rótulo e a ausência do aviso de espera no salão quando o tipo é `takeaway`.

#### **Pré-Condições**

- Pedido criado após selecionar **Para levar**.
- Usuário na tela de confirmação.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Observar o campo **Tipo** | Valor **Para levar**. |
| 2 | Procurar o aviso de mapa/chamada | O bloco **Após o pagamento, aguarde ser chamado...** **não** é exibido. |

#### **Resultados Esperados**

- Instrução de pagamento no balcão permanece visível independentemente do tipo.

#### **Critérios de Aceitação**

- **Tipo** = **Para levar**.
- Sem mensagem de aguardar chamada pelo número.

---

### **CT034 - Voltar da confirmação para a escolha de pagamento**

#### **Objetivo**

Validar a seta de voltar em `PaymentConfirm`.

#### **Pré-Condições**

- Usuário em `/payment/pix/confirm` (ou debit/credit) com pedido vigente.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar na seta de voltar | Navegação para `/payment`. |
| 2 | Observar a tela | As três formas de pagamento continuam disponíveis. |

#### **Resultados Esperados**

- Pedido não é cancelado nem resetado.

#### **Critérios de Aceitação**

- URL `/payment` e mesmo número de pedido.

---

### **CT035 - Fazer novo pedido após confirmação**

#### **Objetivo**

Validar **Fazer Novo Pedido**: limpa estado e volta à home **sem** marcar o pedido como pago.

#### **Pré-Condições**

- Usuário na tela de confirmação com pedido vigente.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Clicar em **Fazer Novo Pedido** | `resetOrder` limpa itens, tipo e pedido atual no estado e no `localStorage`. |
| 2 | Observar a tela | URL `/`. |
| 3 | Iniciar um novo fluxo no cardápio | Carrinho começa vazio. |

#### **Resultados Esperados**

- Diferente de **Cancelar Pedido**, não há `update` de status `cancelled`.
- Status no banco permanece o último gravado (em geral `pending`).

#### **Critérios de Aceitação**

- Tela inicial visível.
- Chaves de persistência do pedido removidas.

---

### **CT036 - Acessar confirmação sem pedido ou sem método**

#### **Objetivo**

Validar proteção da rota `/payment/:method/confirm`.

#### **Pré-Condições**

- Sem `currentOrder`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Acessar `/payment/pix/confirm` | Redirecionamento para `/payment`. |
| 2 | Como não há pedido, o fluxo de `/payment` redireciona para `/` | Usuário termina na tela inicial. |

#### **Resultados Esperados**

- Confirmação não é renderizada sem pedido.

#### **Critérios de Aceitação**

- Não permanece em `/payment/pix/confirm` sem pedido vigente.

---

### 4.8 Persistência e navegação

### **CT037 - Persistência do carrinho após recarregar a página**

#### **Objetivo**

Validar que itens e tipo de pedido sobrevivem ao F5.

#### **Pré-Condições**

- Tipo **Para comer aqui** selecionado.
- Carrinho com pelo menos 1 item.
- Usuário em `/menu` ou `/cart`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Recarregar o navegador (F5) | A aplicação recarrega. |
| 2 | Ir para `/menu` ou `/cart` | Os mesmos itens e quantidades permanecem. |
| 3 | Verificar `localStorage` | `mcbugs-cart-items` e `mcbugs-order-type` populados. |

#### **Resultados Esperados**

- Após criar o pedido com sucesso, `mcbugs-cart-items` é removido; o pedido vigente fica em `mcbugs-current-order`.

#### **Critérios de Aceitação**

- Itens iguais aos de antes do recarregar (antes da finalização).

---

### **CT038 - Persistência do pedido vigente na tela de pagamento**

#### **Objetivo**

Validar que recarregar em `/payment` mantém o pedido.

#### **Pré-Condições**

- Pedido já criado; usuário em `/payment`.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Recarregar a página | A tela de pagamento continua exibindo o mesmo `#{id}` e total. |

#### **Resultados Esperados**

- `createdAt` é reconstituído a partir do ISO salvo no `localStorage`.

#### **Critérios de Aceitação**

- Mesmo número de pedido após F5.

---

### **CT039 - Rota inexistente (404)**

#### **Objetivo**

Validar a página para caminhos não mapeados.

#### **Pré-Condições**

- Aplicação online.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Acessar `/rota-inexistente` | Tela com **404** e **Oops! Page not found**. |
| 2 | Clicar em **Return to Home** | Navegação para `/`. |
| 3 | Observar o console | Log `404 Error: User attempted to access non-existent route:` com o path. |

#### **Resultados Esperados**

- Qualquer path fora das rotas de `App.tsx` cai em `NotFound`.

#### **Critérios de Aceitação**

- Textos 404 visíveis.
- Link **Return to Home** leva à tela inicial.

---

### **CT040 - Fallback de imagem quebrada**

#### **Objetivo**

Validar `onError` das imagens de produto/hero (substituição por `/placeholder.svg`).

#### **Pré-Condições**

- Ambiente em que a imagem original falhe ao carregar (arquivo ausente ou bloqueado), **ou** inspeção do atributo `src` após erro.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Abrir cardápio ou detalhe e forçar falha de carregamento da imagem | O `src` da imagem passa a `/placeholder.svg`. |

#### **Resultados Esperados**

- O mesmo fallback existe em Index (ícones de consumo), Menu (hero), ProductCard, ProductDetail e CartItem.

#### **Critérios de Aceitação**

- Após erro de load, a imagem visível é o placeholder, não um ícone quebrado permanente sem substituição.

---

### **CT041 - Tipo de pedido omitido na criação (padrão dine-in)**

#### **Objetivo**

Validar o fallback `orderType || 'dine-in'` em `createOrder` quando o tipo não está definido.

#### **Pré-Condições**

- Acessar `/menu` **sem** passar pela tela inicial (ou limpar `mcbugs-order-type`).
- Adicionar itens e finalizar com nome válido.
- Supabase acessível.

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Montar o carrinho e finalizar o pedido | Pedido é criado. |
| 2 | Ir até a confirmação e observar **Tipo** | Valor **Comer no local** (`dine-in`). |

#### **Resultados Esperados**

- O cardápio não exige tipo de pedido para navegação; o default só aparece na criação do pedido.

#### **Critérios de Aceitação**

- `order_type` gravado = `dine-in` quando não havia tipo selecionado.

---

## 5. Cobertura Funcional

| ID | Funcionalidade | Cenários positivos | Cenários negativos | Cenários de limite / alternativos | Total de CTs |
|----|----------------|--------------------|--------------------|-----------------------------------|--------------|
| F01 | Tela inicial e tipo de pedido | 3 | 0 | 0 | 3 |
| F02 | Cardápio e categorias | 4 | 0 | 1 | 5 |
| F03 | Detalhe do produto | 3 | 1 | 1 | 5 |
| F04 | Carrinho e CartBar | 5 | 1 | 1 | 7 |
| F05 | Checkout (nome e criação) | 2 | 2 | 1 | 5 |
| F06 | Pagamento | 4 | 2 | 0 | 6 |
| F07 | Confirmação do pedido | 3 | 1 | 1 | 5 |
| F08 | Persistência, 404 e fallback | 3 | 1 | 1 | 5 |
| **Total** | | **27** | **8** | **6** | **41** |

Classificação usada na tabela: **positivo** = fluxo feliz ou exibição correta; **negativo** = dado/rota inválida, bloqueio ou falha de API; **limite/alternativo** = mínimo de quantidade, persistência, fallback, voltar/cancelar já contabilizados na linha quando o CT é predominantemente desse tipo. Os CTs de voltar/cancelar nas seções F04–F07 entram no total da funcionalidade (coluna Total).

Contagem fiel aos identificadores CT001–CT041 (41 casos).

---

## 6. Pontos de Atenção

| ID | Funcionalidade | Ponto de Atenção | Impacto |
|----|----------------|------------------|---------|
| PA001 | Pagamento PIX / cartão | Os arquivos `PaymentPix.tsx` e `PaymentCard.tsx` **não** estão registrados em `App.tsx`. A rota real é `/payment/:method/confirm` → `PaymentConfirm`. | Não testar como fluxo de produção o timer de 5s do PIX nem o cancelamento exclusivo de `PaymentCard`. |
| PA002 | Status pago | `completePayment` (status `paid`) só é chamado em `PaymentPix`, que está fora das rotas. `PaymentConfirm` e **Fazer Novo Pedido** não atualizam o status. | Pedidos tendem a permanecer `pending` no banco após o fluxo oficial. |
| PA003 | Erro no checkout | `Cart.tsx` comenta que o toast de erro é exibido no `CartContext`, mas o contexto apenas faz `console.error`. | Falha de criação pode não ter feedback visual além do drawer reaberto. |
| PA004 | Métodos de pagamento | Na criação, o método é sempre `'pix'` e depois pode ser atualizado na tela de pagamento. | Relatórios ou consultas entre criação e seleção podem ver PIX temporário. |
| PA005 | Nome do cliente | Valor inicial `'Fernando'` na primeira abertura do drawer. | Pode finalizar pedido com nome padrão sem o usuário perceber, na primeira abertura. |
| PA006 | Status Aberto | Texto **Aberto!** é estático; não há regra de horário de funcionamento no código. | Não há CT de estabelecimento fechado. |
| PA007 | Autenticação | Não existe login, perfil ou permissão na aplicação (totem público + RLS permitindo insert/select/update anônimos). | Casos de autenticação não se aplicam. |
| PA008 | Número sequencial | README cita migration `get_next_order_number`; no repositório analisado o `id` da tabela `orders` é `SERIAL`. | O número exibido é o `id` retornado pelo insert. |

Não foram inventadas regras além do que o código e o schema evidenciam.

---

## 7. Resumo

| Métrica | Quantidade |
|---------|------------|
| Funcionalidades analisadas (F01–F08) | 8 |
| Total de casos de teste | 41 |
| Cenários positivos (tabela de cobertura) | 27 |
| Cenários negativos (tabela de cobertura) | 8 |
| Cenários de limite / alternativos (tabela de cobertura) | 6 |
| Pontos de atenção | 8 |

O McBugs cobre o fluxo de totem: tipo de consumo → cardápio → detalhe → carrinho → nome → pagamento no balcão → novo pedido. Não há autenticação. Testes de performance e automação ficaram fora, conforme o pedido.
