# Documento de Casos de Teste de Jornada - Sistema McBugs

**Sistema:** McBugs - Totem de Autoatendimento  
**Tipo:** Testes manuais funcionais a nível de jornada  
**Data de Criação:** 2026-09-13  
**Versão:** 1.0

---

## Escopo

Este documento cobre as duas jornadas principais do totem:

| ID | Jornada | Tipo de pedido persistido | Rótulo na confirmação |
|----|---------|---------------------------|------------------------|
| CT001 | Para comer aqui | `dine-in` | Comer no local |
| CT002 | Para levar | `takeaway` | Para levar |

Não estão no escopo: testes de performance, testes de automação e testes de API isolados.

---

## Fluxo de referência

```
Tela inicial (/)
  → Seleção do tipo de pedido
  → Cardápio (/menu)
  → Detalhe do produto (/product/:id)
  → Carrinho (/cart)
  → Drawer "Finalizar Pedido" (nome do cliente)
  → Pagamento (/payment)
  → Confirmação (/payment/:method/confirm)
  → Fazer Novo Pedido → Tela inicial
```

**Chaves de persistência (localStorage):** `mcbugs-order-type`, `mcbugs-cart-items`, `mcbugs-current-order`.

**Formas de pagamento:** PIX, Cartão de Débito e Cartão de Crédito. Todas redirecionam para `/payment/{pix|debit|credit}/confirm` com a instrução de pagamento no balcão.

---

### **CT001 - Jornada completa "Para comer aqui"**

#### **Objetivo**

Validar ponta a ponta o pedido para consumo no restaurante: da escolha de "Para comer aqui" até a confirmação do pagamento, garantindo que o tipo `dine-in` seja persistido, gravado no pedido e exibido como "Comer no local", incluindo a orientação para aguardar ser chamado pelo número do pedido.

#### **Pré-Condições**

- O sistema McBugs deve estar online e acessível (ex.: `http://localhost:8080`).
- O navegador deve estar em uma sessão limpa: sem itens de carrinho e sem pedido em andamento (ou usar aba anônima / limpar localStorage das chaves `mcbugs-*`).
- A conexão com o backend (Supabase) deve estar ativa para criação do pedido.
- Dados de teste sugeridos:
  - Cliente: `Maria Silva`
  - Itens: 1x Big Mock (R$ 39,90) + 1x Batatas Full Stack (R$ 10,90) + 1x Coca-Crash (R$ 5,90)
  - Total esperado: **R$ 56,80**
  - Pagamento: PIX

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Acessar a URL raiz do sistema (`/`). | A página inicial carrega com o logo, o título **McBugs**, a mensagem **Seja bem-vindo!**, o texto de apoio e as duas opções **Para comer aqui** e **Para levar**. |
| 2 | Clicar no card/botão **Para comer aqui**. | O sistema define o tipo de pedido como `dine-in`, persiste em `mcbugs-order-type` e redireciona para `/menu`. |
| 3 | Verificar o cardápio. | A página `/menu` exibe a imagem de destaque, o nome McBugs, o status **Aberto!**, as abas **Lanches**, **Fritas**, **Bebidas** e **Sobremesas**, e a categoria **Lanches** selecionada por padrão com os lanches em grid (Big Mock, Duplo Deploy, McMerge, McNífico Flaky). A barra inferior do carrinho **não** aparece (carrinho vazio). |
| 4 | Clicar no produto **Big Mock**. | O sistema abre `/product/big-mock` com nome, preço **R$ 39,90**, descrição, lista de ingredientes e seletor de quantidade iniciando em **1**. O botão inferior exibe **Quero • R$ 39,90**. |
| 5 | Manter quantidade 1 e clicar em **Quero**. | O item é adicionado ao carrinho e o usuário retorna para `/menu`. A barra inferior aparece com total **R$ 39,90 / 1 item** e o botão **Ver pedido**. |
| 6 | Selecionar a aba **Fritas**. | A grade passa a exibir Batatas Full Stack, Batatas Refatoradas e Batatas Minificadas. A barra do carrinho permanece visível. |
| 7 | Abrir **Batatas Full Stack**, manter quantidade 1 e clicar em **Quero**. | Retorno ao menu. Barra do carrinho: **R$ 50,80 / 2 itens**. |
| 8 | Selecionar a aba **Bebidas**, abrir **Coca-Crash**, manter quantidade 1 e clicar em **Quero**. | Retorno ao menu. Barra do carrinho: **R$ 56,80 / 3 itens**. |
| 9 | Clicar em **Ver pedido**. | A página `/cart` abre com o título **Meu pedido**, os três itens, subtotais corretos e o card **Total do pedido** com **R$ 56,80**. Existem os botões **Finalizar pedido** e **Continuar comprando**. |
| 10 | Clicar em **Continuar comprando** e, em seguida, voltar ao carrinho pelo **Ver pedido**. | O usuário retorna ao `/menu` sem perder os itens; ao reabrir o carrinho, os três itens e o total **R$ 56,80** permanecem. |
| 11 | Clicar em **Finalizar pedido**. | Abre o drawer **Finalizar Pedido** com o campo **Seu nome** (obrigatório) e os botões **Cancelar** e **Finalizar**. |
| 12 | Tentar finalizar com o campo nome vazio (apagar qualquer valor pré-preenchido) e confirmar. | O envio não é aceito (campo `required`). O usuário permanece no drawer. |
| 13 | Informar o nome **Maria Silva** e clicar em **Finalizar**. | Exibe o estado de carregamento **enviando a cozinha....**. Em seguida, o pedido é criado no backend com `order_type = dine-in`, `customer_name = Maria Silva`, `status = pending` e total **56.80**. O carrinho é esvaziado e o usuário é redirecionado para `/payment`. |
| 14 | Verificar o resumo em `/payment`. | São exibidos o número do pedido (`#` + id), o **Total do pedido R$ 56,80**, as opções **PIX**, **Cartão de Débito** e **Cartão de Crédito** (todas com texto de pagamento no balcão) e o botão **Cancelar Pedido**. |
| 15 | Selecionar **PIX**. | O método de pagamento do pedido é atualizado para `pix` e o usuário é redirecionado para `/payment/pix/confirm`. |
| 16 | Verificar a tela de confirmação. | O cabeçalho exibe **PIX**. O card mostra o mesmo número de pedido e total **R$ 56,80**. O bloco **Pagamento no Balcão** informa para dirigir-se ao balcão e pagar com PIX. Em **Detalhes do pedido**: Cliente **Maria Silva**, Tipo **Comer no local**, Forma de pagamento **PIX**, data/hora no formato pt-BR e a lista **1x Big Mock**, **1x Batatas Full Stack**, **1x Coca-Crash**. |
| 17 | Verificar a orientação exclusiva de consumo no local. | Deve aparecer o aviso: **Após o pagamento, aguarde ser chamado pelo número do seu pedido.** |
| 18 | Clicar em **Fazer Novo Pedido**. | O estado do pedido/carrinho/tipo é limpo (incluindo as chaves `mcbugs-*` no localStorage) e o usuário volta para `/`. As duas opções de jornada voltam a estar disponíveis. |

#### **Resultados Esperados**

- A jornada inicia exclusivamente pela opção **Para comer aqui**.
- O tipo `dine-in` permanece associado ao pedido até a confirmação.
- Os itens escolhidos, quantidades e total **R$ 56,80** são consistentes do cardápio até a tela final.
- O pedido é gravado com nome **Maria Silva**, tipo **dine-in** e método **pix**.
- Na confirmação, o tipo aparece como **Comer no local** e a mensagem de chamada pelo número do pedido é exibida.
- Após **Fazer Novo Pedido**, o totem volta ao estado inicial, pronto para outra jornada.

#### **Critérios de Aceitação**

- O redirecionamento `/` → `/menu` ocorre imediatamente após **Para comer aqui**.
- `localStorage.mcbugs-order-type` contém `dine-in` após a seleção (e permanece até o reset).
- Não é possível finalizar o pedido sem nome.
- O total exibido no menu, no carrinho, no pagamento e na confirmação é **R$ 56,80**.
- Na confirmação, Tipo = **Comer no local** (não "Para levar").
- O aviso "aguarde ser chamado pelo número do seu pedido" está visível.
- O botão **Fazer Novo Pedido** restaura a tela inicial sem resíduos de carrinho ou pedido.

---

### **CT002 - Jornada completa "Para levar"**

#### **Objetivo**

Validar ponta a ponta o pedido para viagem: da escolha de "Para levar" até a confirmação do pagamento, garantindo que o tipo `takeaway` seja persistido, gravado no pedido e exibido como "Para levar", **sem** a orientação de aguardar ser chamado (diferença em relação ao consumo no local).

#### **Pré-Condições**

- O sistema McBugs deve estar online e acessível.
- Sessão limpa: sem carrinho e sem pedido em andamento (ou limpar as chaves `mcbugs-*`).
- Backend (Supabase) disponível para criação do pedido.
- Dados de teste sugeridos:
  - Cliente: `João Pereira`
  - Itens: 2x Duplo Deploy (R$ 41,50 cada) + 1x Casquinha Dark Mode (R$ 3,90)
  - Total esperado: **R$ 86,90**
  - Pagamento: Cartão de Débito

#### **Passos**

| **Id** | **Ação** | **Resultado Esperado** |
|--------|----------|------------------------|
| 1 | Acessar a URL raiz (`/`). | Página inicial com **Para comer aqui** e **Para levar** visíveis e clicáveis. |
| 2 | Clicar no card/botão **Para levar**. | O sistema define o tipo como `takeaway`, persiste em `mcbugs-order-type` e redireciona para `/menu`. |
| 3 | Verificar o cardápio. | `/menu` carrega normalmente (categorias, status **Aberto!**, Lanches como aba padrão). A escolha "Para levar" não altera o cardápio; o diferencial é o tipo persistido. |
| 4 | Abrir o produto **Duplo Deploy**. | `/product/duplo-deploy` exibe nome, preço **R$ 41,50**, descrição, ingredientes e quantidade **1**. O botão mostra **Quero • R$ 41,50**. |
| 5 | Aumentar a quantidade para **2** (botão "+") e clicar em **Quero**. | O botão passa a exibir **Quero • R$ 83,00** antes do clique. Após adicionar, retorna ao menu. Barra do carrinho: **R$ 83,00 / 2 itens**. |
| 6 | Selecionar a aba **Sobremesas**, abrir **Casquinha Dark Mode**, manter quantidade 1 e clicar em **Quero**. | Retorno ao menu. Barra: **R$ 86,90 / 3 itens**. |
| 7 | Clicar em **Ver pedido**. | `/cart` lista **Duplo Deploy** com quantidade 2 (subtotal **R$ 83,00**) e **Casquinha Dark Mode** quantidade 1 (subtotal **R$ 3,90**). **Total do pedido: R$ 86,90**. |
| 8 | No Duplo Deploy, clicar em "-" uma vez e em seguida em "+". | A quantidade vai para 1 (total temporário **R$ 45,40**) e depois volta para 2. O total retorna a **R$ 86,90**. Nenhum item é removido indevidamente. |
| 9 | Clicar em **Finalizar pedido**. | Abre o drawer **Finalizar Pedido** com campo de nome obrigatório. |
| 10 | Informar o nome **João Pereira** e clicar em **Finalizar**. | Exibe **enviando a cozinha....**. O pedido é criado com `order_type = takeaway`, `customer_name = João Pereira`, `status = pending` e total **86.90**. O usuário vai para `/payment`. |
| 11 | Verificar o resumo em `/payment`. | Número do pedido, total **R$ 86,90** e as três formas de pagamento estão visíveis. |
| 12 | Selecionar **Cartão de Débito**. | O método do pedido é atualizado para `debit` e ocorre redirecionamento para `/payment/debit/confirm`. |
| 13 | Verificar a tela de confirmação. | Cabeçalho **Cartão de Débito**. Instrução **Pagamento no Balcão** com Cartão de Débito. Detalhes: Cliente **João Pereira**, Tipo **Para levar**, Forma de pagamento **Cartão de Débito**, itens **2x Duplo Deploy** e **1x Casquinha Dark Mode**, total **R$ 86,90**. |
| 14 | Verificar a ausência da orientação de consumo no local. | **Não** deve aparecer o aviso "Após o pagamento, aguarde ser chamado pelo número do seu pedido." O tipo não pode aparecer como "Comer no local". |
| 15 | Usar o botão voltar (seta) da confirmação. | O usuário retorna para `/payment` com o mesmo pedido e total. O pedido não é cancelado. |
| 16 | Selecionar novamente **Cartão de Débito** (ou PIX) e reabrir a confirmação. | A confirmação continua exibindo Tipo **Para levar** e os mesmos itens/total. |
| 17 | Clicar em **Fazer Novo Pedido**. | Carrinho, tipo de pedido e pedido atual são limpos. O usuário volta para `/` e pode iniciar uma nova jornada (comer aqui ou levar). |

#### **Resultados Esperados**

- A jornada inicia exclusivamente pela opção **Para levar**.
- O tipo `takeaway` permanece associado ao pedido até a confirmação.
- Quantidade maior que 1 (2x Duplo Deploy) é refletida no preço do botão **Quero**, na barra do carrinho, no carrinho e na confirmação.
- O pedido é gravado com nome **João Pereira**, tipo **takeaway** e método **debit**.
- Na confirmação, o tipo aparece como **Para levar** e **não** há mensagem de chamada por número de pedido.
- É possível voltar de `/payment/:method/confirm` para `/payment` sem perder o pedido.
- Após **Fazer Novo Pedido**, o totem volta ao estado inicial.

#### **Critérios de Aceitação**

- O redirecionamento `/` → `/menu` ocorre imediatamente após **Para levar**.
- `localStorage.mcbugs-order-type` contém `takeaway` após a seleção.
- Total consistente **R$ 86,90** em todas as telas da jornada.
- Na confirmação, Tipo = **Para levar** (não "Comer no local").
- O aviso de aguardar ser chamado **não** é exibido nesta jornada.
- Alterar quantidade no carrinho recalcula o total corretamente.
- **Fazer Novo Pedido** limpa o estado e libera uma nova escolha de jornada.

---

## Diferenças obrigatórias entre as jornadas

| Ponto de verificação | Para comer aqui (CT001) | Para levar (CT002) |
|----------------------|-------------------------|--------------------|
| Botão na home | Para comer aqui | Para levar |
| Valor em `mcbugs-order-type` | `dine-in` | `takeaway` |
| Campo `order_type` no pedido | `dine-in` | `takeaway` |
| Rótulo "Tipo" na confirmação | Comer no local | Para levar |
| Aviso "aguarde ser chamado pelo número do seu pedido" | Deve aparecer | Não deve aparecer |
| Cardápio, carrinho e métodos de pagamento | Iguais | Iguais |

---

## Observações para execução manual

1. O campo de nome pode abrir com valor pré-preenchido na primeira abertura do drawer; o analista deve garantir o nome definido no caso de teste.
2. Se o backend estiver indisponível, o fluxo para em **Finalizar** (drawer de checkout). Registrar como bloqueio, não como falha da jornada de tipo de pedido.
3. PIX, débito e crédito usam a mesma tela de confirmação (`/payment/:method/confirm`). O método escolhido deve aparecer no cabeçalho e nos detalhes; o tipo de pedido não deve mudar ao trocar o método.
4. Cancelar o pedido em `/payment` deve voltar à home e limpar o estado; esse caminho é complementar e pode ser usado como evidência extra, sem substituir os passos de sucesso das jornadas.
