<script setup>
defineProps({
  utilizador: Object,
  totalAtividades: Number,
  pendentes: Number,
  concluidas: Number,
  paginaAtual: String,
  modoEscuro: Boolean
})

defineEmits(['logout', 'mudar-pagina', 'alternar-tema'])
</script>

<template>
  <nav 
    class="navbar navbar-expand-lg shadow-sm mb-4 rounded-4 px-4 py-3"
    :class="modoEscuro ? 'navbar-dark' : 'navbar-light bg-white border'"
    :style="modoEscuro ? 'background-color: #16191f; border: 1px solid #262b34;' : ''"
  >
    <div class="container-fluid">
      <a 
        href="#" 
        @click.prevent="$emit('mudar-pagina', 'dashboard')" 
        class="navbar-brand fw-bold fs-3 d-flex align-items-center gap-2" 
        :class="modoEscuro ? 'text-white' : 'text-dark'"
        style="letter-spacing: -0.5px;"
      >
        <i class="bi bi-mortarboard-fill" style="color: #1a73e8;"></i>
        <span>EscolaAtiva</span>
      </a>

      <div class="d-flex align-items-center gap-2 gap-md-3">
        <!-- Resumo rápido -->
        <div class="d-none d-lg-flex gap-2 me-2">
          <span class="badge px-3 py-2 rounded-pill fw-semibold" style="background-color: #1a73e8; color: #ffffff;">
            Total: {{ totalAtividades }}
          </span>
          <span class="badge px-3 py-2 rounded-pill fw-semibold" style="background-color: #8ab4f8; color: #0f1115;">
            Pendentes: {{ pendentes }}
          </span>
          <span 
            class="badge px-3 py-2 rounded-pill fw-semibold"
            :class="modoEscuro ? 'bg-dark text-light border border-secondary' : 'bg-light text-dark border'"
          >
            Concluídas: {{ concluidas }}
          </span>
        </div>

        <!-- Botão Modo Escuro / Claro -->
        <button 
          @click="$emit('alternar-tema')" 
          class="btn btn-sm rounded-circle d-flex align-items-center justify-content-center border"
          :class="modoEscuro ? 'btn-dark border-secondary' : 'btn-light'"
          style="width: 36px; height: 36px;"
          :title="modoEscuro ? 'Mudar para Modo Claro' : 'Mudar para Modo Escuro'"
        >
          <i :class="modoEscuro ? 'bi bi-sun-fill text-warning' : 'bi bi-moon-stars-fill text-dark'"></i>
        </button>

        <!-- Botão de Perfil com Avatar -->
        <button 
          @click="$emit('mudar-pagina', paginaAtual === 'perfil' ? 'dashboard' : 'perfil')" 
          class="btn btn-sm rounded-pill px-3 py-1 fw-semibold d-flex align-items-center gap-2 border"
          :class="paginaAtual === 'perfil' ? '' : (modoEscuro ? 'btn-dark border-secondary text-white' : 'btn-light text-dark')"
          :style="paginaAtual === 'perfil' ? 'background-color: #8ab4f8; color: #0f1115; border-color: #8ab4f8;' : ''"
        >
          <img 
            v-if="utilizador && utilizador.avatar" 
            :src="utilizador.avatar" 
            alt="Avatar" 
            class="rounded-circle object-fit-cover"
            style="width: 26px; height: 26px;"
          >
          <i v-else class="bi bi-person-circle fs-6"></i>
          <span>{{ utilizador.nome }}</span>
        </button>

        <!-- Botão Sair -->
        <button 
          @click="$emit('logout')" 
          class="btn btn-sm px-3 rounded-pill d-flex align-items-center gap-1"
          :class="modoEscuro ? 'btn-outline-light' : 'btn-outline-dark'"
        >
          <i class="bi bi-box-arrow-right"></i>
          <span class="d-none d-sm-inline">Sair</span>
        </button>
      </div>
    </div>
  </nav>
</template>