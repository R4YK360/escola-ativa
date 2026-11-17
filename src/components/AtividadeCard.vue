<script setup>
defineProps({
  atividade: {
    type: Object,
    required: true
  }
})

defineEmits(['eliminar', 'mudar-estado', 'editar'])
</script>

<template>
  <div class="card border-0 shadow-sm mb-3 rounded-4 bg-white p-2">
    <div class="card-body d-flex justify-content-between align-items-center">
      <div>
        <h5 class="mb-1 fw-bold" style="color: #0f1115;">{{ atividade.titulo }}</h5>
        <p class="mb-2 text-muted small">{{ atividade.descricao }}</p>
        <div class="d-flex align-items-center gap-3">
          <span class="small text-secondary">
            <i class="bi bi-calendar-event me-1"></i>{{ atividade.data }}
          </span>
          <span 
            :class="atividade.estado === 'concluida' ? 'badge bg-success bg-opacity-10 text-success px-3 py-1 rounded-pill' : 'badge px-3 py-1 rounded-pill'" 
            :style="atividade.estado === 'pendente' ? 'background-color: #8ab4f8; color: #0f1115;' : ''"
          >
            {{ atividade.estado.toUpperCase() }}
          </span>
        </div>
      </div>

      <div class="d-flex gap-2">
        <button 
          @click="$emit('mudar-estado', atividade.id)" 
          class="btn btn-sm btn-light border rounded-circle d-flex align-items-center justify-content-center"
          style="width: 36px; height: 36px;"
          title="Alternar estado"
        >
          <i class="bi bi-arrow-repeat text-dark"></i>
        </button>
        <button 
          @click="$emit('editar', atividade)" 
          class="btn btn-sm btn-light border rounded-circle d-flex align-items-center justify-content-center"
          style="width: 36px; height: 36px;"
          title="Editar atividade"
        >
          <i class="bi bi-pencil text-primary"></i>
        </button>
        <button 
          @click="$emit('eliminar', atividade.id)" 
          class="btn btn-sm btn-light border rounded-circle d-flex align-items-center justify-content-center"
          style="width: 36px; height: 36px;"
          title="Eliminar atividade"
        >
          <i class="bi bi-trash3 text-danger"></i>
        </button>
      </div>
    </div>
  </div>
</template>