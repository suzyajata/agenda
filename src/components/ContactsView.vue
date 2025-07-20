<template>
    <div class="container mt-4">
        <h1>{{ title }}</h1>
        <p>
            <button type="button" class="btn btn-primary" @click="goToNew()">
                ➕ Nuevo Contacto
            </button>
        </p>
        
        <!-- Filtros -->
        <div class="row mb-3">
            <div class="col-md-6">
                <div class="input-group">
                    <span class="input-group-text">🔍 Nombre</span>
                    <input type="search" v-model="nombreABuscar" class="form-control" 
                           placeholder="Buscar por nombre...">
                </div>
            </div>
            <div class="col-md-6">
                <div class="input-group">
                    <span class="input-group-text">📧 Email</span>
                    <input type="search" v-model="emailABuscar" class="form-control" 
                           placeholder="Buscar por email...">
                </div>
            </div>
        </div>

        <!-- Contador de resultados -->
        <div class="mb-2">
            <small class="text-muted">
                Mostrando {{ getContactos.length }} de {{ contactos.length }} contactos
            </small>
        </div>

        <!-- Tabla de contactos -->
        <div class="table-responsive">
            <table class="table table-bordered border-primary table-striped">
                <thead class="table-dark">
                    <tr>
                        <th scope="col">ID</th>
                        <th scope="col">Nombre</th>
                        <th scope="col">Email</th>
                        <th scope="col">Teléfono</th>
                        <th scope="col">País</th>
                        <th scope="col">Ciudad</th>
                        <th scope="col">Acciones</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="(contacto, index) in getContactos" :key="contacto.id">
                        <th scope="row">{{ contacto.id }}</th>
                        <td>{{ contacto.name }}</td>
                        <td>{{ contacto.email }}</td>
                        <td>{{ contacto.phone || '-' }}</td>
                        <td>{{ contacto.country || '-' }}</td>
                        <td>{{ contacto.city || '-' }}</td>
                        <td>
                            <div class="btn-group" role="group">
                                <button type="button" class="btn btn-sm btn-outline-primary" 
                                        @click="abrirModal(index)" title="Editar">
                                    ✏️
                                </button>
                                <button type="button" class="btn btn-sm btn-outline-danger" 
                                        @click="eliminar(index)" title="Eliminar">
                                    🗑️
                                </button>
                            </div>
                        </td>
                    </tr>
                </tbody>
            </table>
            
            <div v-if="getContactos.length === 0" class="text-center text-muted p-4">
                <div class="alert alert-info">
                    <h5>📋 No se encontraron contactos</h5>
                    <p class="mb-0">
                        {{ contactos.length === 0 ? 'No hay contactos registrados.' : 'No hay contactos que coincidan con los filtros aplicados.' }}
                    </p>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal Bootstrap -->
    <div class="modal fade" id="modalContactoEditar" tabindex="-1" 
         aria-labelledby="modalContactoEditarLabel" aria-hidden="true" ref="modalRef">
        <div class="modal-dialog modal-lg">
            <div class="modal-content">
                <div class="modal-header">
                    <h5 class="modal-title" id="modalContactoEditarLabel">
                        {{ modalMode === 'editar' ? '✏️ Editar Contacto' : '➕ Nuevo Contacto' }}
                    </h5>
                    <button type="button" class="btn-close" data-bs-dismiss="modal" 
                            aria-label="Cerrar"></button>
                </div>
                <div class="modal-body">
                    <!-- Componente ContactoEditor -->
                    <ContactoEditor v-if="modalMode === 'editar' && contactoSeleccionado" 
                                    :contacto="contactoSeleccionado" 
                                    @update="guardarEdicion"
                                    @cancelar="cerrarModal" />
                    <!-- Componente NuevoContacto -->
                    <NuevoContacto v-if="modalMode === 'crear'" 
                                   @created="agregarNuevo($event)" />
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import ContactoEditor from '../components/ContactoEditor.vue';
import NuevoContacto from '../components/NuevoContacto.vue';

export default {
    name: 'ContactsView',
    data() {
        return {
            title: '📞 Gestión de Contactos',
            contactos: [
                {
                    id: 1,
                    name: "Alice Johnson",
                    email: "alice.johnson@example.com",
                    address: "123 Maple Street",
                    phone: "123-456-7890",
                    country: "USA",
                    city: "New York"
                },
                {
                    id: 2,
                    name: "Bob Smith",
                    email: "bob.smith@example.com",
                    address: "456 Oak Avenue",
                    phone: "987-654-3210",
                    country: "Canada",
                    city: "Toronto"
                },
                {
                    id: 3,
                    name: "Carol White",
                    email: "carol.white@example.com",
                    address: "789 Pine Road",
                    phone: "555-123-4567",
                    country: "UK",
                    city: "London"
                },
                {
                    id: 4,
                    name: "David Brown",
                    email: "david.brown@example.com",
                    address: "321 Elm Street",
                    phone: "444-555-6666",
                    country: "Australia",
                    city: "Sydney"
                },
                {
                    id: 5,
                    name: "Emily Davis",
                    email: "emily.davis@example.com",
                    address: "654 Spruce Lane",
                    phone: "333-444-5555",
                    country: "USA",
                    city: "Los Angeles"
                }
            ],
            modalBootstrapInstance: null,
            contactoSeleccionado: null,
            indiceSeleccionado: 0,
            nombreABuscar: "",
            emailABuscar: "",
            modalMode: "crear"
        }
    },
    components: {
        ContactoEditor,
        NuevoContacto,
    },
    mounted() {
        this.$nextTick(() => {
            if (this.$refs.modalRef) {
                this.modalBootstrapInstance = new bootstrap.Modal(this.$refs.modalRef);
            } else {
                console.error('No se encontró el ref modalRef');
            }
        });
    },
    methods: {
        goToNew() {
            this.modalMode = "crear";
            if (this.modalBootstrapInstance) {
                this.modalBootstrapInstance.show();
            } else {
                console.error('modalBootstrapInstance no está inicializado');
            }
        },
        abrirModal(index) {
            this.contactoSeleccionado = null;
            this.indiceSeleccionado = index;
            this.modalMode = "editar";
            setTimeout(() => {
                if (this.modalBootstrapInstance) {
                    this.modalBootstrapInstance.show();
                    this.contactoSeleccionado = { ...this.getContactos[index] };
                } else {
                    console.error('modalBootstrapInstance no está inicializado');
                }
            });
        },
        cerrarModal() {
            if (this.modalBootstrapInstance) {
                this.modalBootstrapInstance.hide();
            }
        },
        guardarEdicion(contactoEditado) {
            console.log('Contacto editado:', contactoEditado);
            // Encontrar el índice real en el array original
            const realIndex = this.contactos.findIndex(c => c.id === contactoEditado.id);
            if (realIndex !== -1) {
                this.contactos[realIndex] = contactoEditado;
                alert('✅ Contacto actualizado correctamente');
            }
            this.cerrarModal();
        },
        eliminar(index) {
            const contacto = this.getContactos[index];
            if (confirm(`¿Está seguro de eliminar el contacto "${contacto.name}"?`)) {
                const realIndex = this.contactos.findIndex(c => c.id === contacto.id);
                if (realIndex !== -1) {
                    this.contactos.splice(realIndex, 1);
                    alert('✅ Contacto eliminado correctamente');
                }
            }
        },
        agregarNuevo(nuevoContacto) {
            const maxId = this.contactos.length > 0 ? Math.max(...this.contactos.map(contacto => contacto.id)) : 0;
            nuevoContacto.id = maxId + 1;
            this.contactos.push(nuevoContacto);
            console.log('Nuevo contacto agregado:', nuevoContacto);
            alert('✅ Contacto agregado correctamente');
            this.cerrarModal();
        }
    },
    computed: {
        getContactos() {
            let result = [...this.contactos];
            
            // Filtrar por nombre
            if (this.nombreABuscar !== "") {
                result = result.filter((contacto) => {
                    const nombre = contacto.name || "";
                    const textoBusqueda = this.nombreABuscar || "";
                    return nombre.toLowerCase().includes(textoBusqueda.toLowerCase());
                });
            }
            
            // Filtrar por email
            if (this.emailABuscar !== "") {
                result = result.filter((contacto) => {
                    const email = contacto.email || "";
                    const textoBusqueda = this.emailABuscar || "";
                    return email.toLowerCase().includes(textoBusqueda.toLowerCase());
                });
            }
            
            return result;
        }
    }
}
</script>

<style scoped>
.btn-group .btn {
    margin-right: 0;
}

.table-responsive {
    border-radius: 0.375rem;
    overflow: hidden;
}

.alert {
    border-radius: 0.375rem;
}
</style>