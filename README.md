<!DOCTYPE html>
<html lang="pt-BR">
<html lang="en-EUA>
<head>
  <meta charset="UTF-8">
  <title>Versadigital</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 0;
      padding: 20px;
      background-color: #f7f7f7;
    }
    h1, h2 {
      text-align: center;
    }
    #produtos {
      display: grid;
      grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
      gap: 20px;
      margin-bottom: 40px;
    }
    .produto {
      background: white;
      border: 1px solid #ddd;
      border-radius: 8px;
      padding: 15px;
      text-align: center;
      box-shadow: 0px 2px 5px rgba(0,0,0,0.1);
    }
    .produto img {
      max-width: 100%;
      height: 150px;
      object-fit: cover;
      margin-bottom: 10px;
      border-radius: 8px;
    }
    .produto input {
      width: 60px;
      margin: 5px 0;
    }
    .produto button {
      background-color: #28a745;
      color: white;
      border: none;
      padding: 8px 12px;
      border-radius: 5px;
      cursor: pointer;
    }
    .produto button:hover {
      background-color: #218838;
    }
    .sacola, .formulario {
      background: white;
      border: 1px solid #ddd;
      border-radius: 8px;
      padding: 20px;
      margin-bottom: 20px;
      box-shadow: 0px 2px 5px rgba(0,0,0,0.1);
      max-width: 600px;
      margin-left: auto;
      margin-right: auto;
    }
    #sacola li {
      list-style: none;
      margin: 5px 0;
    }
    input, select {
      width: 100%;
      padding: 8px;
      margin: 5px 0 10px;
      border: 1px solid #ccc;
      border-radius: 5px;
    }
    button[type="submit"] {
      background-color: #007bff;
      padding: 10px;
    }
    button[type="submit"]:hover {
      background-color: #0056b3;
    }
  </style>
</head>
<body>

  <h1>Versatilly</h1>

  <div id="produtos"></div>

  <div class="sacola">
    <h2>Sacola</h2>
    <ul id="sacola"></ul>
    <p><strong>Total:</strong> R$ <span id="total">0.00</span></p>
  </div>

  <div class="formulario">
    <h2>Finalizar Pedido</h2>
    <form id="formulario">
      <input type="text" id="nome" placeholder="Nome" required>
      <input type="text" id="endereco" placeholder="Endereço" required>
      <select id="pagamento" required>
        <option value="">Forma de pagamento</option>
        <option value="Dinheiro">Dinheiro</option>
        <option value="Cartão">Cartão</option>
        <option value="Pix">Pix</option>
      </select>
      <button type="submit">Enviar Pedido no WhatsApp</button>
    </form>
  </div>

  <script>
    let produtos = JSON.parse(localStorage.getItem('produtos')) || [
      { id: 1, nome: "Conjunto (blusa + calça) vermelho", preco: 320.00, imagem: 'https://images.app.goo.gl/MEWR4QjudASMsT4v5'}
    ];
    let sacola = [];

    function renderizarProdutos() {
      const produtosDiv = document.getElementById('produtos');
      produtosDiv.innerHTML = '';

      produtos.forEach(produto => {
        const card = document.createElement('div');
        card.className = 'produto';
        card.innerHTML = `
          <img src="${produto.imagem}" alt="${produto.nome}">
          <h3>${produto.nome}</h3>
          <p>R$ ${produto.preco.toFixed(2)}</p>
          <input type="number" id="qtd-${produto.id}" min="1" value="1">
          <br>
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

      const url = `https://wa.me/+5585991526255text=${encodeURIComponent(mensagem)}`;
      window.open(url, '_blank');
    });

    renderizarProdutos();
  </script>

</body>
</html>
