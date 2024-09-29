<template>
    <div>
        <div id="app">
            <div class="container mt-2">
                <div class="row janela-principal">

                    <!-- Coluna Esquerda -->
                    <div class="col-md-4 lista-de-conversas p-0">
                        <div class="barra-superior mb-2 p-3">
                            <img src="https://via.placeholder.com/40" alt="Perfil de usuário"
                                class="rounded-circle mr-2" style="margin-right: 10px;">
                            <span>Meu Perfil</span>
                        </div>
                        <div class="contatos-lista">
                             <item-lista-de-conversa 
                                v-for="(conversa, indice) in conversas"
                                :key="indice" 
                                :mensagemAtiva="indice === indiceAtivo"
                                :nomeDoUsuario="conversa.usuario"
                                :ultimaMensagem="conversa.mensagens[0]?.conteudo"
                                :foto="conversa.foto"
                                :temNotificacao="conversa.temNotificacao" 
                                @click.native="indiceAtivo = indice"
                            />

                        </div>
                    </div>

                    <!-- Coluna Direita -->
                    <div class="col-md-8 conversa-ativa p-0">
                        <!-- Barra Superior -->
                        <div class="barra-superior mb-2 p-3 d-flex align-items-center" style="margin-right: 10px;" >
                            <img :src="conversas[indiceAtivo].foto"
                                alt="Foto de perfil de {{ conversas[indiceAtivo].usuario }}" class="rounded-circle mr-2"
                                width="40" style="margin-right: 10px;" >
                            <span>{{ conversas[indiceAtivo].usuario }}</span>
                        </div>
                        <!-- Mensagens -->
                        <div class="lista-mensagens p-3">
                            <mensagem-da-conversa-ativa v-for="(mensagem, indice_) in conversas[indiceAtivo].mensagens"
                                :key="indice_" :conteudo="mensagem.conteudo" :horario="mensagem.horario"
                                :verde="mensagem.verde" />
                        </div>
                        <!-- Barra Inferior -->
                        <div class="barra-inferior p-2">
                            <input type="text" class="form-control" v-on:keyup.enter="enviarMensagem"
                                placeholder="Insira sua mensagem" v-model="conteudoASerEnviado"
                                aria-label="Digite sua mensagem">
                        </div>
                    </div>

                </div>
            </div>
        </div>
    </div>
</template>

<script>
import conversasIniciais from '../dados.js';
import ItemDaListaDeConversa from '../components/ItemDaListaDeConversa.vue';
import MensagemDaConversaAtiva from '../components/MensagemDaConversaAtiva.vue';

export default {
    name: "App",
    data() {
        return {
            conversas: conversasIniciais,
            indiceAtivo: 0,
            conteudoASerEnviado: "",
        }
    },
    components: {
        "item-lista-de-conversa": ItemDaListaDeConversa,
        "mensagem-da-conversa-ativa": MensagemDaConversaAtiva,
    },

    methods: {
        enviarMensagem() {
            if (this.conteudoASerEnviado.trim() === "") {
                alert("Por favor, insira uma mensagem.");
                return;
            }

            const horarioAtual = new Date().toLocaleTimeString([], { hour: '2-digit', minute: '2-digit' });

            const novaMensagem = {
                horario: horarioAtual,
                conteudo: this.conteudoASerEnviado,
                verde: true,
            };

            // Adiciona a nova mensagem à conversa ativa
            this.conversas[this.indiceAtivo].mensagens.push(novaMensagem);
            this.conteudoASerEnviado = "";

            // Reseta as notificações para outras conversas
            this.conversas.forEach((conversa, index) => {
                if (index !== this.indiceAtivo) {
                    conversa.temNotificacao = true; // Define notificação para outras conversas
                }
            });
                // Rolagem automática para a última mensagem
            this.$nextTick(() => {
                const mensagensContainer = document.querySelector('.lista-mensagens');
                mensagensContainer.scrollTop = mensagensContainer.scrollHeight; 
            });
        }
    }

};
</script>

<style>
/* Estilos principais */
.janela-principal {
    min-height: 600px;
    box-shadow: 0 3rem 3rem -1rem rgba(10, 10, 10, .2);
    border-radius: 10px;
    background: #1b1b1b;
    color: white; 
    margin-top: 0; 
}

/* Barra Superior */
.barra-superior {
    background-color: #075E54; /* Cor do WhatsApp */
    color: white;
    border-bottom: 1px solid rgb(200, 200, 200);
}
.barra-superior img {
    border-radius: 50%;
}
.barra-superior span {
    font-weight: 500;
    font-size: 18px;
}

/* Coluna Direita */
.conversa-ativa {
    background: #2b2b2b; 
    position: relative;
    border-radius: 0 10px 10px 0;
    display: flex;
    flex-direction: column; 
}


/* Contatos - Coluna Esquerda */
.contatos-lista {
    height: 500px; 
    overflow-y: auto;
}
.contatos-lista::-webkit-scrollbar {
    width: 8px;
}
.contatos-lista::-webkit-scrollbar-thumb {
    background-color: rgba(255, 255, 255, 0.3); /* cor da scrollbar */
}

/* Barra Inferior */
.barra-inferior {
    background: #333; 
}
.barra-inferior input {
    border-radius: 25px;
    padding: 10px;
    margin: 0;
    background: #444; 
    color: white; 
}

/* Customizações de mensagens */
.lista-mensagens::-webkit-scrollbar {
    width: 8px;
}
.lista-mensagens::-webkit-scrollbar-thumb {
    background-color: rgba(53, 52, 52, 0.897);
    border-radius: 4px;
}

/* Estilos do Item da Lista de Conversa */
.item {
    padding: 15px 20px;
    border-bottom: 1px solid #F2F2F2;  
    cursor: pointer;
}
.item:hover {
    background: rgb(37, 36, 36);;
}
.item.ativo {
    background: rgb(49, 48, 48);
}

/* Estilos das Mensagens */


.lista-mensagens {
    height: 400px; 
    overflow-y: auto; 
    display: flex;
    flex-direction: column;
    padding: 10px; 
}

.mensagem {
    font-size: 14px;
    box-shadow: #030303 0px 1px 0.5px 0px; /* sombra na msg*/
    background: rgb(71, 63, 63);
    margin: 5px 0; /* Margem entre as mensagens */
    padding: 5px 10px;
    border-radius: 10px;
    width: fit-content; 
}
.mensagem.verde {
    background: rgb(220, 248, 198); /*  mensagens enviadas */
    align-self: flex-end; 
    color: black
}
.mensagem span {
    margin-left: 15px;
    font-size: 12px;
    color: gray; /* cor da hora */
}

/* Estilos adicionais */
.subtitle {
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
    color: gray; /* cor da ultima mensagem, embaixo do nome */
}


</style>
