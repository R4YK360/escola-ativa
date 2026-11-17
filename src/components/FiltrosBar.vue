<script setup>
defineProps({
  pesquisa: String,
  estadoFiltro: String
})

defineEmits(['update:pesquisa', 'update:estadoFiltro', 'abrir-modal'])
</script>

<template>
  <div class="card border-0 shadow-sm mb-4 rounded-4 bg-white p-2">
    <div class="card-body">
      <div class="row g-3 align-items-center">
        <!-- Campo de pesquisa -->
        <div class="col-md-5">
          <div class="input-group">
            <span class="input-group-text bg-white border-secondary-subtle border-end-0 text-muted">
              <i class="bi bi-search"></i>
            </span>
            <input 
              :value="pesquisa" 
              @input="$emit('update:pesquisa', $event.target.value)" 
              type="text" 
              class="form-control border-secondary-subtle border-start-0 text-dark" 
              placeholder="Pesquisar por título..."
            >
          </div>
        </div>

        <!-- Seletor de estado -->
        <div class="col-md-4">
          <select 
            :value="estadoFiltro" 
            @change="$emit('update:estadoFiltro', $event.target.value)" 
            class="form-select border-secondary-subtle fw-semibold text-dark"
          >
            <option value="todas">Todas as Atividades</option>
            <option value="pendente">Pendentes</option>
            <option value="concluida">Concluídas</option>
          </select>
        </div>

        <!-- Botão para abrir a janela de Nova Atividade -->
        <div class="col-md-3 text-end">
          <button 
            @click="$emit('abrir-modal')" 
            class="btn w-100 py-2 fw-bold text-white rounded-pill shadow-sm d-flex align-items-center justify-content-center gap-2" 
            style="background-color: #1a73e8;"
          >
            <i class="bi bi-plus-lg"></i>
            <span>Nova Atividade</span>
          </button>
        </div>
      </div>
    </div>
  </div>
</template>