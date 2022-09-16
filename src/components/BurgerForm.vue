<template>
  <div>
    <message 
      :msg="msg"
      v-show="msg"
    />
    <div>
      <form id="burger-form" @submit.prevent="createBurger">
        <div class="input-container">
          <label for="nome">Nome do cliente</label>
          <input 
            type="text" 
            id="nome" 
            name="nome" 
            v-model="nome" 
            placeholder="Digite o seu nome"
          />
        </div>
        <div class="input-container">
          <label for="pao">Escolha o pão:</label>
          <select name="pao" id="pao" v-model="pao">
            <option value="" disabled>Selecione o seu pão</option>
            <option v-for="pao in paes" :key="pao.id" :value="pao.tipo">{{ pao.tipo }}</option> 
          </select>
        </div>
        <div class="input-container">
          <label for="carne">Escolha a carne do seu burger:</label>
          <select name="carne" id="carne" v-model="carne">
            <option value="">Selecione o tipo de carne</option>
            <option v-for="carne in carnes" :key="carne.id" :value="carne.tipo">{{ carne.tipo }}</option> 
          </select>
        </div>
        <div id="opcionais-container" class="input-container">
          <label id="opcionais-title" for="opcionais">Selecione os opcionais:</label>
          <div class="checkbox-container" v-for="opcional in opcionais_data" :key="opcional.id">
            <input type="checkbox" name="opcionais" v-model="opcionais" :value="opcional.tipo" />
            <span>{{ opcional.tipo }}</span>
          </div>
        </div>
        <div class="input-container">
          <input type="submit" class="submit-btn" value="criar meu burguer" />
        </div>
      </form>  
    </div>
  </div>
</template>

<script>
import Message from './Message.vue'

export default {
  components: { Message },
  name: 'BurgerForm',
  data() {
    return {
      paes: null,
      carnes: null,
      opcionais_data: null,
      nome: null,
      pao: null,
      carne: null,
      opcionais: [],
      msg: null,
    }
  },
  methods: {
    async getIngredients() {

      const req = await fetch('http://localhost:3000/ingredientes')
      const data = await req.json()

      this.paes = data.paes
      this.carnes = data.carnes
      this.opcionais_data = data.opcionais
    },

    async createBurger() {

      const data = {
        nome: this.nome,
        carne: this.carne,
        pao: this.pao,
        opcionais: Array.from(this.opcionais),
        status: "Solicitado",
      }
      
      const dataJson = JSON.stringify(data)

      const req = await fetch('http://localhost:3000/burgers', {
        method: "POST",
        headers: { "Content-Type" : "application/json" },
        body: dataJson
      })

      const res = await req.json()

      //emitir uma mensagem no sistema
      this.msg = `Pedido  N ${res.id} realizado com sucesso!`

      //limpar msg
      setTimeout(() => this.msg = '', 3000)

      //limpar os campos
      this.nome = ''
      this.carne = ''
      this.pao = ''
      this.opcionais = ''

    }
  },

  mounted() {
    this.getIngredients()
  }
}
</script>

<style scope>
  #burger-form {
    max-width: 400px;
    margin: 0 auto;
  }

  .input-container {
    display: flex;
    flex-direction: column;
    margin-bottom: 20px;
  }

  label {
    font-weight: bold;
    margin-bottom: 15px;
    color: #222;
    padding: 5px 10px;
    border-left: 4px solid #fcba03;
  }

  input, select {
    padding: 5px 10px;
    width: 100%;
  }

  #opcionais-container {
    flex-direction: row;
    flex-wrap: wrap;
  }

  #opcionais-title {
    width: 100%;
  }

  .checkbox-container {
    display: flex;
    align-items: flex-start;
    width: 50%; 
  }

  .checkbox-container span, 
  .checkbox-container input {
    width: auto;
  }

  .checkbox-container span {
    font-weight: bold;
    margin-left: 5px;
  }

  .submit-btn {
    background-color: #222;
    color: #fcba03;
    font-weight: bold;
    border: 2px solid #222;
    padding: 10px;
    font-size: 16px;
    margin: 0 auto;
    cursor: pointer;
    transition: .5s;
  }

  .submit-btn:hover {
    background-color: transparent;
    color: #222;
  }

</style>