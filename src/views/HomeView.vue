<template>
  <div class="home">
    <div class="container mt-5">
      <h1 class="mb-4">Gestión de Contactos</h1>
      <div class="row">
        <div class="col-md-8 mx-auto">
          <div class="card">
            <div class="card-body">
              <h5 class="card-title">Sistema de Gestión de Contactos con Vue 3</h5>
              <p class="card-text">
                Esta aplicación permite gestionar contactos de manera completa con las siguientes funcionalidades:
              </p>
              <ul class="list-group list-group-flush mb-3">
                <li class="list-group-item">✅ Listar contactos</li>
                <li class="list-group-item">✅ Agregar nuevos contactos</li>
                <li class="list-group-item">✅ Editar contactos existentes</li>
                <li class="list-group-item">✅ Eliminar contactos</li>
                <li class="list-group-item">✅ Filtrar por nombre y email</li>
              </ul>
              <router-link to="/contacts" class="btn btn-primary">
                Ir a Gestión de Contactos
              </router-link>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'HomeView'
}
</script>

<style scoped>
.container {
  max-width: 800px;
}
</style>