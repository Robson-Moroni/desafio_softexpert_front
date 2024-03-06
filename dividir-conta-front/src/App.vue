<template>
  <div id="app">
  <form @submit.prevent="submitForm">
    <div class="container">
      <h1>Divisão de Conta</h1>
      <div class="form-group">
        <label for="total">Total da conta:</label>
        <input type="number" id="total" v-model="conta.total" required>
      </div>
      <div class="form-group">
        <label for="discount">Desconto:</label>
        <input type="number" id="discount" v-model="conta.desconto.valor" placeholder="Valor em reais" step="0.01">
        <select id="discount-type" v-model="conta.desconto.tipo">
          <option value="PORCENTAGEM">Porcentagem</option>
          <option value="VALOR">Valor</option>
        </select>
      </div>
      <div class="form-group">
        <label for="addition">Acrescimo:</label>
        <input type="number" id="addition" v-model="conta.acrescimo.valor" placeholder="Valor em reais" step="0.01">
        <select id="addition-type" v-model="conta.acrescimo.tipo">
          <option value="PORCENTAGEM">Porcentagem</option>
          <option value="VALOR">Valor</option>
        </select>
      </div>
      <div class="form-group">
        <label for="pedidos">Pedidos:</label>
        <ul>
          <li v-for="(pedido, index) in conta.pedidos" :key="index">
            <div class="pedido-container">
              <div class="form-group">
                <label for="pedido-total">Total:</label>
                <input type="number" :id="'pedido-total-' + index" v-model="pedido.total" required>
              </div>
              <div class="form-group">
                <label for="pedido-pessoa">Pertence a:</label>
                <input type="text" :id="'pedido-pessoa-' + index" v-model="pedido.pessoa" required>
              </div>
              <div class="form-group">
                <label for="itens">Itens:</label>
                <ul>
                  <li v-for="(item, itemIndex) in pedido.itens" :key="itemIndex">
                    <input type="text" :id="'pedido-item-' + index + '-' + itemIndex" v-model="pedido.itens[itemIndex].descricao" placeholder="Descrição" required>
                    <input type="number" :id="'pedido-item-value-' + index + '-' + itemIndex" v-model="pedido.itens[itemIndex].valor" placeholder="Valor" step="0.01" required>
                    <input type="number" :id="'pedido-item-quantity-' + index + '-' + itemIndex" v-model="pedido.itens[itemIndex].quantidade" placeholder="Quantidade" required>
                  </li>
                </ul>
                <button @click.prevent="addItemToPedido(index)" class="add-button">Adicionar item</button>
              </div>
            </div>
          </li>
        </ul>
        <button @click.prevent="addPedido" class="add-button">Adicionar pedido</button>
      </div>
      <button type="submit" class="submit-button">Calcular</button>
    </div>
  </form>
  <div v-if="divisaoConta" class="container result">
    <h1>Divisão da conta:</h1>
    <ul>
      <li v-for="(divisao, index) in divisaoConta.divisoes" :key="index">
       {{ divisao.pessoa }}: R$ {{ divisao.totalPago.toFixed(2) }}
      </li>
    </ul>
  </div>
  </div>
</template>

<script>

import axios from 'axios';

export default {
 data() {
   return {
     conta: {
       total: 0,
       desconto: {
         tipo: 'PORCENTAGEM',
         valor: 0
       },
       acrescimo: {
         tipo: 'PORCENTAGEM',
         valor: 0
       },
       pedidos: [{
         total: 0,
         pessoa: '',
         itens: [{
           descricao: '',
           valor: 0,
           quantidade: 0
         }]
       }]
     },
     divisaoConta: null
   }
 },
 methods: {
   addItemToPedido(index) {
     this.conta.pedidos[index].itens.push({
       descricao: '',
       valor: 0,
       quantidade: 0
     });
   },
   addPedido() {
     this.conta.pedidos.push({
       total: 0,
       pessoa: '',
       itens: [{
         descricao: '',
         valor: 0,
         quantidade: 0
       }]
     });
   },
   async submitForm() {
     try {
       const response = await axios.get('http://localhost:8080/api/divisao-conta/dividir');
       console.log(response.data)
      } catch (error) { console.error(error); } 
    } 
  } 
}
</script>




<style scoped>
 body {
 font-family: 'Helvetica Neue', Helvetica, Arial, sans-serif;
 margin: 0;
 padding: 0;
 background: #fafafa;
}

#app {
 display: flex;
 flex-direction: column;
 align-items: center;
 padding: 2rem;
}

.container {
 width: 100%;
 max-width: 600px;
 background: white;
 box-shadow: 0 1px 2px rgba(0, 0, 0, 0.1);
 padding: 2rem;
 margin-bottom: 2rem;
 border-radius: 4px;
}

.form-group {
 margin-bottom: 1rem;
}

label {
 display: block;
 margin-bottom: 0.5rem;
 font-weight: bold;
}

input[type="text"],
input[type="number"] {
 width: 100%;
 padding: 0.5rem;
 border: 1px solid #ddd;
 border-radius: 4px;
 font-size: 1rem;
 line-height: 1.5;
}

input[type="number"]::-webkit-inner-spin-button,
input[type="number"]::-webkit-outer-spin-button {
 -webkit-appearance: none;
 margin: 0;
}

.add-button {
 background: #007bff;
 color: white;
 padding: 0.5rem 1rem;
 border: 0;
 border-radius: 4px;
 font-size: 1rem;
 cursor: pointer;
 margin-top: 1rem;
}

.submit-button {
 background: #0095ff;
 color: white;
 padding: 0.5rem 1rem;
 border: 0;
 border-radius: 4px;
 font-size: 1rem;
 cursor: pointer;
 margin-top: 1rem;
}

.pedido-container {
 border: 1px solid #ddd;
 border-radius: 4px;
 padding: 1rem;
 margin-bottom: 1rem;
}
</style>
