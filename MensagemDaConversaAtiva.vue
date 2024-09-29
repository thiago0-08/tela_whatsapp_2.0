<template>
    <div class="mensagem" :class="{ verde: verde }">
        {{ conteudo }}
        <span>{{ horario }}</span>
    </div>
</template>

<script>
export default {
    name: "MensagemDaConversaAtiva",
    props: ['conteudo', 'horario', 'verde'],
};
</script>

<style>
.mensagem {
    font-size: 14px;
    box-shadow: rgba(0, 0, 0, 0.13) 0px 1px 0.5px 0px;
    background: #33383b;
    margin: 5px 100px;
    padding: 10px;
    border-radius: 10px;
    width: fit-content;
    color: white;
}
.mensagem.verde {
    background: #056162;
    align-self: flex-end;
}
.mensagem span {
    margin-left: 15px;
    font-size: 12px;
    color: gray;
}
</style>
