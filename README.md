<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <title>Versatilly</title>
  <style>
    /* Seu CSS aqui (estilize como quiser) */
  </style>
</head>
<body>

  <h1>Versatilly</h1>

  <div id="produtos" style="display: grid; grid-template-columns: repeat(auto-fit, minmax(200px, 1fr)); gap: 20px;"></div>

  <h2>Sacola</h2>
  <ul id="sacola"></ul>
  <p><strong>Total:</strong> R$ <span id="total">0.00</span></p>

  <h2>Finalizar Pedido</h2>
  <form id="formulario">
    <input type="text" id="nome" placeholder="Nome" required><br>
    <input type="text" id="endereco" placeholder="Endereço" required><br>
    <select id="pagamento" required>
      <option value="">Forma de pagamento</option>
      <option value="Dinheiro">Dinheiro</option>
      <option value="Pix">Pix</option>
    </select><br><br>
    <button type="submit">Enviar Pedido no WhatsApp</button>
  </form>

  <script>
    let produtos = JSON.parse(localStorage.getItem('produtos')) || [
      { id: 1, nome: "Produto Exemplo", preco: 10.00, imagem: "https://via.placeholder.com/150" }
    ];
    let sacola = [];

    function renderizarProdutos() {
      const produtosDiv = document.getElementById('produtos');
      produtosDiv.innerHTML = '';

      produtos.forEach(produto => {
        const card = document.createElement('div');
        card.innerHTML = `
          <img src="${produto.imagem}" alt="${produto.nome}" style="width:100%">
          <h3>${produto.nome}</h3>
          <p>R$ ${produto.preco.toFixed(2)}</p>
          <input type="number" id="qtd-${produto.id}" min="1" value="1">
          <button onclick="adicionarSacola(${produto.id})">Adicionar</button>
        `;
        produtosDiv.appendChild(card);
      });
    }

    function adicionarSacola(id) {
      const produto = produtos.find(p => p.id === id);
      const quantidade = parseInt(document.getElementById(`qtd-${id}`).value);

      for (let i = 0; i < quantidade; i++) {
        sacola.push(produto);
      }
      renderizarSacola();
    }

    function renderizarSacola() {
      const sacolaUl = document.getElementById('sacola');
      const totalSpan = document.getElementById('total');
      sacolaUl.innerHTML = '';

      let total = 0;
      sacola.forEach(item => {
        const li = document.createElement('li');
        li.textContent = item.nome + ' - R$ ' + item.preco.toFixed(2);
        sacolaUl.appendChild(li);
        total += item.preco;
      });

      totalSpan.textContent = total.toFixed(2);
    }

    document.getElementById('formulario').addEventListener('submit', function(event) {
      event.preventDefault();
      const nome = document.getElementById('nome').value;
      const endereco = document.getElementById('endereco').value;
      const pagamento = document.getElementById('pagamento').value;

      let mensagem = `*Pedido feito pela Loja Virtual*\n\n*Nome:* ${nome}\n*Endereço:* ${endereco}\n*Forma de Pagamento:* ${pagamento}\n\n*Itens:* \n`;
      sacola.forEach(item => {
        mensagem += `- ${item.nome} - R$ ${item.preco.toFixed(2)}\n`;
      });

      const total = sacola.reduce((sum, item) => sum + item.preco, 0);
      mensagem += `\n*Total:* R$ ${total.toFixed(2)}`;

      const url = `https://wa.me/5585991769080text=${encodeURIComponent(mensagem)}`;
      window.open(url, '_blank');
    });

    renderizarProdutos();
  </script>

</body>
</html>
