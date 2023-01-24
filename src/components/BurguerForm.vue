<template>
    <div>
        <MensagemAviso :msg="msg" v-show="msg" />
        <div>
            <form id="burguer-form" @submit="createBurguer">
                <div class="input-container">
                    <label for="nome">Nome do cliente:</label>
                    <input type="text" id="nome" name="nome" v-model="nome" placeholder="Digite o seu nome">
                </div>

                <div class="input-container">
                    <label for="pao">Escolha o pão:</label>
                    <select name="pao" id="pao" v-model="pao">
                        <option v-for="pao in paes_consulta" :key="pao.id" :value="pao.tipo">{{ pao.tipo }}</option>
                    </select>
                </div>

                <div class="input-container">
                    <label for="carne">Escolha sua carne:</label>
                    <select name="carne" id="carne" v-model="carne">
                        <option v-for="carne in carnes_consulta" :key="carne.id" :value="carne.tipo">{{ carne.tipo }}</option>
                    </select>
                </div>

                <div id="opcionais-container" class="input-container">
                    <label id="opcionais-title" for="opcionais">Selecione os opcionais:</label>
                    <div class="checkbox-container" v-for="opcional in opcionais_consulta" :key="opcional.id">
                        <input type="checkbox" name="opcionais" v-model="opcionais" :value="opcional.tipo">
                        <span>{{ opcional.tipo }}</span>
                    </div>
                </div>

                <div class="input-container">
                    <input type="submit" class="submit-btn" value="Confirmar pedido">
                </div>
            </form>
        </div>
    </div>
</template>

<script>
import MensagemAviso from '@/components/MensagemAviso.vue';

export default {
    name: 'BurguerForm',
    components: { MensagemAviso },
    data() {
        return {
            paes_consulta: null,
            carnes_consulta: null,
            opcionais_consulta: null,
            nome: '',
            pao: null,
            carne: null,
            opcionais: [],
            msg: null
        }
    },
    methods: {
        async getIngredientes() {
            const req = await fetch("http://localhost:3000/ingredientes");
            const data = await req.json();
            
            this.paes_consulta = data.paes;
            this.carnes_consulta = data.carnes;
            this.opcionais_consulta = data.opcionais;
        },
        async createBurguer(e) {
            e.preventDefault();
            
            const data = {
                nome: this.nome,
                carne: this.carne,
                pao: this.pao,
                opcionais: Array.from(this.opcionais),
                status: "Solicitado",
            }
            const dataJson = JSON.stringify(data);
            const req = await fetch('http://localhost:3000/burgers', {
                method: 'POST',
                headers: { "Content-type": "application/json" },
                body: dataJson
            });

            const res = await req.json();

            // exibe mensagem de conclusao
            this.msg = `Pedido No ${res.id} realizado com sucesso!`;
            
            // limpa mensagem de conclusao apos 4s
            setTimeout(() => this.msg = '', 4000);

            // limpa os campos apos inserir
            this.nome = '';
            this.carne = '';
            this.pao = '';
            this.opcionais = '';
        }
    },
    mounted() {
        this.getIngredientes();
    }
}
</script>

<style scoped>
#burguer-form {
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
    width: 300px;
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
    margin-bottom: 20px;
}

.checkbox-container span,
.checkbox-container input {
    width: auto;
}

.checkbox-container span {
    margin-left: 6px;
    font-weight: bold;
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

.submit-btn:hover{
    background-color: transparent;
    color: #222;
}
</style>
