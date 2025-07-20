<template>
    <div class="container">
        <h1>{{ title }}</h1>
        <p>
            <button type="button" class="btn btn-primary" @click="goToNew()">
                Nuevo Contacto
            </button>
        </p>
        
        <!-- Filtros -->
        <div class="mb-3">
            <div class="input-group">
                <span class="input-group-text">Buscar por nombre</span>
                <input type="search" v-model="nombreABuscar" class="form-control" 
                       placeholder="Escriba el nombre a buscar...">
            </div>
        </div>
        
        <div class="mb-3">
            <div class="input-group">
                <span class="input-group-text">Buscar por email</span>
                <input type="search" v-model="emailABuscar" class="form-control" 
                       placeholder="Escriba el email a buscar...">
            </div>
        </div>

        <!-- Tabla de contactos -->
        <div>
            <table class="table table-bordered border-primary">
                <thead>
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
                        <td>{{ contacto.phone }}</td>
                        <td>{{ contacto.country }}</td>
                        <td>{{ contacto.city }}</td>
                        <td>
                            <button type="button" class="btn btn-sm btn-primary me-2" 
                                    @click="abrirModal(index)">
                                Editar
                            </button>
                            <button type="button" class="btn btn-sm btn-danger" 
                                    @click="eliminar(index)">
                                Eliminar
                            </button>
                        </td>
                    </tr>
                </tbody>
            </table>
            
            <div v-if="getContactos.length === 0" class="text-center text-muted">
                <p>No se encontraron contactos que coincidan con los filtros.</p>
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
                        {{ modalMode === 'editar' ? 'Editar Contacto' : 'Nuevo Contacto' }}
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
            title: 'Gestión de Contactos',
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
            }
            this.cerrarModal();
        },
        eliminar(index) {
            if (confirm('¿Está seguro de eliminar este contacto?')) {
                const contactoAEliminar = this.getContactos[index];
                const realIndex = this.contactos.findIndex(c => c.id === contactoAEliminar.id);
                if (realIndex !== -1) {
                    this.contactos.splice(realIndex, 1);
                }
            }
        },
        agregarNuevo(nuevoContacto) {
            const maxId = Math.max(...this.contactos.map(contacto => contacto.id));
            nuevoContacto.id = maxId + 1;
            this.contactos.push(nuevoContacto);
            console.log('Nuevo contacto agregado:', nuevoContacto);
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
    },
    props: {},
    emits: []
}
</script>

<style scoped>
.btn {
    padding: 0.375rem 0.75rem;
    margin: 0.125rem;
    border: 1px solid transparent;
    border-radius: 0.25rem;
    font-size: 0.875rem;
    text-decoration: none;
    display: inline-block;
    cursor: pointer;
}

.btn-primary {
    background-color: #0d6efd;
    border-color: #0d6efd;
    color: white;
}

.btn-danger {
    background-color: #dc3545;
    border-color: #dc3545;
    color: white;
}

.btn-sm {
    padding: 0.25rem 0.5rem;
    font-size: 0.75rem;
}

.me-2 {
    margin-right: 0.5rem;
}

.mb-3 {
    margin-bottom: 1rem;
}

.input-group {
    position: relative;
    display: flex;
    flex-wrap: wrap;
    align-items: stretch;
    width: 100%;
}

.input-group-text {
    display: flex;
    align-items: center;
    padding: 0.375rem 0.75rem;
    font-size: 1rem;
    font-weight: 400;
    line-height: 1.5;
    color: #212529;
    text-align: center;
    white-space: nowrap;
    background-color: #e9ecef;
    border: 1px solid #ced4da;
    border-radius: 0.25rem 0 0 0.25rem;
}

.form-control {
    display: block;
    width: 100%;
    padding: 0.375rem 0.75rem;
    font-size: 1rem;
    font-weight: 400;
    line-height: 1.5;
    color: #212529;
    background-color: #fff;
    background-image: none;
    border: 1px solid #ced4da;
    border-radius: 0 0.25rem 0.25rem 0;
    transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
}

.table {
    width: 100%;
    margin-bottom: 1rem;
    color: #212529;
    border-collapse: collapse;
}

.table th,
.table td {
    padding: 0.5rem;
    vertical-align: top;
    border: 1px solid #dee2e6;
}

.table thead th {
    background-color: #f8f9fa;
    font-weight: 600;
}

.table-bordered {
    border: 1px solid #dee2e6;
}

.border-primary {
    border-color: #0d6efd !important;
}

.text-center {
    text-align: center;
}

.text-muted {
    color: #6c757d;
}
</style>