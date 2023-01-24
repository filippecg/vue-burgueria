<template>
    <div id="burguer-table">
        <MensagemAviso :msg="msg" v-show="msg" />
        <div>
            <div id="burguer-table-heading">
                <div class="order-id">#</div>
                <div>Cliente:</div>
                <div>Pão:</div>
                <div>Carne:</div>
                <div>Opcionais:</div>
                <div>Ações:</div>
            </div>
        </div>

        <div id="burguer-table-rows">
            <div class="burguer-table-row" v-for="pedido in pedidos" :key="pedido.id">
                <div class="order-number">{{ pedido.id }}</div>
                <div>{{ pedido.nome }}</div>
                <div>{{ pedido.pao }}</div>
                <div>{{ pedido.carne }}</div>
                <div>
                    <ul>
                        <li v-for="(opcional, index) in pedido.opcionais" :key="index">{{ opcional }}</li>
                    </ul>
                </div>
                <div>
                    <select name="status" class="status" @change="updatePedido($event, pedido.id)">
                        <option :value="info.tipo" v-for="info in status" :key="info.tipo" :selected="pedido.status == info.tipo">
                            {{ info.tipo }}
                        </option>
                    </select>
                    <button class="delete-btn" @click="deletePedido(pedido.id)">Cancelar</button>
                </div>
            </div>
        </div>

    </div>
</template>

<script>
import MensagemAviso from '@/components/MensagemAviso.vue';

export default {
    name: 'DashboardPedidos',
    components: { MensagemAviso },
    data() {
        return {
            pedidos: null,
            status: [],
            paes: [],
            carnes: [],
            msg: null
        }
    },
    methods: {
        async getPedidos() {
            const req = await fetch("http://localhost:3000/burgers");
            this.pedidos = await req.json();
        },

        async getStatus() {
            const req = await fetch("http://localhost:3000/status");
            this.status = await req.json();
        },
        async deletePedido(id) {
            const req = await fetch(`http://localhost:3000/burgers/${id}`, {
                method: 'DELETE'
            });
            const res = await req.json();

            // exibe mensagem de conclusao
            this.msg = `Pedido cancelado com sucesso!`;
            
            // limpa mensagem de conclusao apos 3s
            setTimeout(() => this.msg = '', 3000);

            this.getPedidos();
        },
        async updatePedido(event, id) {
            const option = event.target.value;
            const dataJson = JSON.stringify({ status: option });
            const req = await fetch(`http://localhost:3000/burgers/${id}`, {
                method: 'PATCH',
                headers: { "Content-type": "application/json" },
                body: dataJson
            });
            const res = await req.json();

            // exibe mensagem de conclusao
            this.msg = `Pedido No ${res.id} atualizado com sucesso!`;
            
            // limpa mensagem de conclusao apos 3s
            setTimeout(() => this.msg = '', 3000);
        }
    },
    mounted() {
        this.getPedidos();
        this.getStatus();
    }
}
</script>

<style scoped>
#burguer-table {
    max-width: 1200px;
    margin: 0 auto;
}

#burguer-table-heading,
#burguer-table-rows,
.burguer-table-row {
    display: flex;
    flex-wrap: wrap;
}

#burguer-table-heading {
    font-weight: bold;
    padding: 12px;
    border-bottom: 3px solid #333;
}

#burguer-table-heading div,
.burguer-table-row div {
    width: 19%;
}

.burguer-table-row {
    width: 100%;
    padding: 12px;
    border-bottom: 1px solid #ccc;
}

#burguer-table-heading .order-id,
.burguer-table-row .order-number {
    width: 5%;
}

select {
    padding: 12px 6px;
    margin-right: 12px;
}

.delete-btn {
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

.delete-btn:hover {
    background-color: transparent;
    color: #222;
}
</style>
