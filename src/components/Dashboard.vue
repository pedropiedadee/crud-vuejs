<template>
  <div id="burguer-table">
    <Message :msg="msg" v-show="msg"/>
    <div>
      <div id="burguer-table-heading">
        <div class="order-id">#:</div>
        <div>Cliente:</div>
        <div>Pão:</div>
        <div>Carne:</div>
        <div>Opcionais:</div>
        <div>Ações:</div>
      </div>
    </div>
    <div id="burguer-table-rows">
      <div class="burguer-table-row" v-for="burger in burgers" :key="burger.id">
        <div class="order-number">{{ burger.id }}</div>
        <div>{{ burger.nome }}</div>
        <div>{{ burger.pao }}</div>
        <div>{{ burger.carne }}</div>
        <div>
          <ul>
            <li v-for="(opcional, index) in burger.opcionais" :key="index">
            {{ opcional }}
          </li>
          </ul>
        </div>
        <div>
          <select name="status" class="status" @change="updateBurger($event, burger.id)">
            <option value="">Selecione</option>
            <option v-for="s in status" :key="s.id" :value="s.tipo" :selected="burger.status == s.tipo">
              {{ s.tipo }}
            </option>
          </select>
          <button class="delete-btn" @click="deleteBurger(burger.id)">Cancelar</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import Message from "./Message.vue";

  export default {
    name: "Dashboard",
    data() {
      return {
        burgers: null,
        burger_id: null,
        status: [],
        msg: null
      }
    },components: {
      Message
    }
    ,
    methods: {
      async getPedidos() {

        const req = await fetch("http://localhost:3000/burgers");

        const data = await req.json();

        this.burgers = data;


        // Resgatar os status
        this.getStatus();
      },
      async getStatus() {

        const req = await fetch("http://localhost:3000/status");

        const data = await req.json();

        this.status = data;
      },
      async deleteBurger(id) {
        const req = await fetch(`http://localhost:3000/burgers/${id}`, {
          method: "DELETE"
        });
      
        const res = await req.json();

        //Colocando a mensagem no msg vindo de data
        this.msg = `Pedido Removido com sucesso!`


        // Tirar a mensagem
        setTimeout(() => this.msg = "", 3000)
        
        this.getPedidos();
      
      },
      async updateBurger(event, id) {
        const option = event.target.value;

        const dataJson = JSON.stringify({ status: option });

        const req = await fetch(`http://localhost:3000/burgers/${id}`,{
          method: "PATCH",
          headers: { "Content-Type": "application/json"},
          body: dataJson
        });

        const res = await req.json();

         //Colocando a mensagem no msg vindo de data
         this.msg = `O Pedido N°${id} foi atualizado para "${res.status}"!`


        // Tirar a mensagem
        setTimeout(() => this.msg = "", 3000)

        console.log(res)

        
      }
    },
    mounted() {
      this.getPedidos();
    }
  }
</script>

<style scoped>
  #burguer-table {
    max-width: 1200px;
    margin: 0 auto
  }

  #burguer-table-heading,
  #burguer-table-rows, .burguer-table-row {
    display: flex;
    flex-wrap: wrap;
  }

  #burguer-table-heading {
    font-weight: bold;
    padding: 12px;
    border-bottom: 3px solid #333;
  }

  #burguer-table-heading div, .burguer-table-row div {
    width: 19%;
  }
  
  .burguer-table-row {
    width: 100%;
    padding: 12px;
    border-bottom: 1px solid #ccc;
  }

  #burguer-table-heading .order-id,
  .burguer-table-row .order-number{
    width: 5%;
  }

  select {
    padding: 12px 6px;
    margin-right: 12px;
  }

  .delete-btn{
    background-color: #222;
    color: #fcba03;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    margin: 0 auto;
    cursor: pointer;
    transition: .5s;
  }

  .delete-btn:hover{
    background-color: transparent;
    color: #222;
  }
</style>