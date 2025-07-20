<template>
    <div class="contacts-container">
        <div class="container mt-4">
            <div class="header-section">
                <h1 class="main-title">{{ title }}</h1>
                <p>
                    <button type="button" class="btn btn-add-contact" @click="goToNew()">
                        ➕ Nuevo Contacto
                    </button>
                </p>
            </div>
            
            <!-- Filtros -->
            <div class="filters-section">
                <div class="row mb-3">
                    <div class="col-md-4">
                        <div class="filter-group">
                            <span class="filter-icon">🔍</span>
                            <input type="search" v-model="nombreABuscar" class="form-control custom-input" 
                                   placeholder="Buscar por nombre...">
                        </div>
                    </div>
                    <div class="col-md-4">
                        <div class="filter-group">
                            <span class="filter-icon">📧</span>
                            <input type="search" v-model="emailABuscar" class="form-control custom-input" 
                                   placeholder="Buscar por email...">
                        </div>
                    </div>
                    <div class="col-md-4">
                        <div class="filter-group">
                            <span class="filter-icon">🌍</span>
                            <select v-model="paisSeleccionado" class="form-select custom-select">
                                <option value="">Todos los países</option>
                                <option v-for="pais in paisesUnicos" :key="pais" :value="pais">{{ pais }}</option>
                            </select>
                        </div>
                    </div>
                </div>
                
                <div class="row mb-3">
                    <div class="col-md-4">
                        <div class="filter-group">
                            <span class="filter-icon">🏙️</span>
                            <input type="search" v-model="ciudadABuscar" class="form-control custom-input" 
                                   placeholder="Buscar por ciudad...">
                        </div>
                    </div>
                    <div class="col-md-8 d-flex align-items-end">
                        <button type="button" class="btn btn-clear-filters" @click="limpiarFiltros">
                            🗑️ Limpiar Filtros
                        </button>
                    </div>
                </div>
            </div>

            <!-- Contador de resultados -->
            <div class="results-counter mb-3">
                <span class="counter-badge">
                    Mostrando {{ getContactos.length }} de {{ contactos.length }} contactos
                </span>
            </div>

            <!-- Tabla de contactos -->
            <div class="table-container">
                <div class="table-responsive">
                    <table class="table custom-table">
                        <thead class="table-header">
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
                            <tr v-for="(contacto, index) in getContactos" :key="contacto.id" class="table-row">
                                <th scope="row" class="id-column">{{ contacto.id }}</th>
                                <td class="name-column">{{ contacto.name }}</td>
                                <td class="email-column">{{ contacto.email }}</td>
                                <td>{{ contacto.phone || '-' }}</td>
                                <td>
                                    <span v-if="contacto.country" class="country-badge">{{ contacto.country }}</span>
                                    <span v-else>-</span>
                                </td>
                                <td>{{ contacto.city || '-' }}</td>
                                <td>
                                    <div class="action-buttons" role="group">
                                        <button type="button" class="btn btn-edit" 
                                                @click="abrirModal(index)" title="Editar">
                                            ✏️
                                        </button>
                                        <button type="button" class="btn btn-delete" 
                                                @click="eliminar(index)" title="Eliminar">
                                            🗑️
                                        </button>
                                    </div>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                    
                    <div v-if="getContactos.length === 0" class="no-results">
                        <div class="alert-custom">
                            <h5>📋 No se encontraron contactos</h5>
                            <p class="mb-0">
                                {{ contactos.length === 0 ? 'No hay contactos registrados.' : 'No hay contactos que coincidan con los filtros aplicados.' }}
                            </p>
                        </div>
                    </div>
                </div>
            </div>
        </div>

        <!-- Modal Bootstrap -->
        <div class="modal fade" id="modalContactoEditar" tabindex="-1" 
             aria-labelledby="modalContactoEditarLabel" aria-hidden="true" ref="modalRef">
            <div class="modal-dialog modal-lg">
                <div class="modal-content custom-modal">
                    <div class="modal-header custom-modal-header">
                        <h5 class="modal-title" id="modalContactoEditarLabel">
                            {{ modalMode === 'editar' ? '✏️ Editar Contacto' : '➕ Nuevo Contacto' }}
                        </h5>
                        <button type="button" class="btn-close custom-close" data-bs-dismiss="modal" 
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
            paisSeleccionado: "",
            ciudadABuscar: "",
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
        },
        limpiarFiltros() {
            this.nombreABuscar = "";
            this.emailABuscar = "";
            this.paisSeleccionado = "";
            this.ciudadABuscar = "";
        }
    },
    computed: {
        paisesUnicos() {
            const paises = this.contactos
                .map(contacto => contacto.country)
                .filter(country => country && country.trim() !== '')
                .filter((country, index, self) => self.indexOf(country) === index)
                .sort();
            return paises;
        },
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

            // Filtrar por país
            if (this.paisSeleccionado !== "") {
                result = result.filter((contacto) => {
                    return contacto.country === this.paisSeleccionado;
                });
            }

            // Filtrar por ciudad
            if (this.ciudadABuscar !== "") {
                result = result.filter((contacto) => {
                    const ciudad = contacto.city || "";
                    const textoBusqueda = this.ciudadABuscar || "";
                    return ciudad.toLowerCase().includes(textoBusqueda.toLowerCase());
                });
            }
            
            return result;
        }
    }
}
</script>

<style scoped>
/* Colores inspirados en la agenda azul */
:root {
    --primary-blue: #2c5aa0;
    --secondary-blue: #4472c4;
    --light-blue: #dce6f2;
    --accent-blue: #1f4788;
    --text-blue: #1a365d;
    --border-blue: #b3c6e7;
    --hover-blue: #f0f4ff;
}

.contacts-container {
    background: linear-gradient(135deg, #f8faff 0%, #e8f2ff 100%);
    min-height: 100vh;
    padding-bottom: 2rem;
}

.header-section {
    background: linear-gradient(135deg, var(--primary-blue) 0%, var(--secondary-blue) 100%);
    color: white;
    padding: 2rem;
    border-radius: 15px;
    margin-bottom: 2rem;
    box-shadow: 0 8px 25px rgba(44, 90, 160, 0.3);
}

.main-title {
    font-size: 2.5rem;
    font-weight: 700;
    margin-bottom: 1rem;
    text-shadow: 0 2px 4px rgba(0,0,0,0.3);
}

.btn-add-contact {
    background: linear-gradient(135deg, #ffffff 0%, #f0f4ff 100%);
    color: var(--primary-blue);
    border: 2px solid rgba(255,255,255,0.3);
    padding: 12px 24px;
    border-radius: 25px;
    font-weight: 600;
    transition: all 0.3s ease;
    box-shadow: 0 4px 15px rgba(255,255,255,0.2);
}

.btn-add-contact:hover {
    background: white;
    transform: translateY(-2px);
    box-shadow: 0 6px 20px rgba(255,255,255,0.3);
}

.filters-section {
    background: white;
    padding: 1.5rem;
    border-radius: 15px;
    box-shadow: 0 4px 20px rgba(44, 90, 160, 0.1);
    margin-bottom: 1.5rem;
    border: 1px solid var(--light-blue);
}

.filter-group {
    position: relative;
    display: flex;
    align-items: center;
}

.filter-icon {
    position: absolute;
    left: 12px;
    z-index: 3;
    font-size: 1.1rem;
}

.custom-input, .custom-select {
    padding-left: 45px !important;
    border: 2px solid var(--border-blue);
    border-radius: 10px;
    transition: all 0.3s ease;
    background: white;
}

.custom-input:focus, .custom-select:focus {
    border-color: var(--secondary-blue);
    box-shadow: 0 0 0 3px rgba(68, 114, 196, 0.1);
    background: var(--hover-blue);
}

.btn-clear-filters {
    background: linear-gradient(135deg, #ff6b6b 0%, #ee5a24 100%);
    color: white;
    border: none;
    padding: 8px 16px;
    border-radius: 8px;
    font-weight: 500;
    transition: all 0.3s ease;
}

.btn-clear-filters:hover {
    transform: translateY(-1px);
    box-shadow: 0 4px 12px rgba(255, 107, 107, 0.3);
}

.results-counter {
    text-align: center;
}

.counter-badge {
    background: linear-gradient(135deg, var(--primary-blue) 0%, var(--secondary-blue) 100%);
    color: white;
    padding: 8px 20px;
    border-radius: 25px;
    font-size: 0.9rem;
    font-weight: 500;
    box-shadow: 0 3px 10px rgba(44, 90, 160, 0.3);
}

.table-container {
    background: white;
    border-radius: 15px;
    overflow: hidden;
    box-shadow: 0 8px 25px rgba(44, 90, 160, 0.15);
    border: 1px solid var(--light-blue);
}

.custom-table {
    margin-bottom: 0;
    border: none;
}

.table-header {
    background: linear-gradient(135deg, var(--primary-blue) 0%, var(--secondary-blue) 100%);
    color: white;
}

.table-header th {
    border: none;
    padding: 1rem 0.75rem;
    font-weight: 600;
    text-align: center;
    position: relative;
}

.table-row {
    transition: all 0.3s ease;
    border-bottom: 1px solid var(--light-blue);
}

.table-row:hover {
    background: var(--hover-blue);
    transform: scale(1.01);
    box-shadow: 0 4px 15px rgba(44, 90, 160, 0.1);
}

.id-column {
    background: linear-gradient(135deg, var(--light-blue) 0%, #ffffff 100%);
    color: var(--text-blue);
    font-weight: 700;
    text-align: center;
}

.name-column {
    font-weight: 600;
    color: var(--text-blue);
}

.email-column {
    color: var(--secondary-blue);
    font-style: italic;
}

.country-badge {
    background: linear-gradient(135deg, var(--secondary-blue) 0%, var(--primary-blue) 100%);
    color: white;
    padding: 4px 12px;
    border-radius: 15px;
    font-size: 0.8rem;
    font-weight: 500;
}

.action-buttons {
    display: flex;
    gap: 0.5rem;
    justify-content: center;
}

.btn-edit {
    background: linear-gradient(135deg, #17a2b8 0%, #138496 100%);
    color: white;
    border: none;
    padding: 6px 12px;
    border-radius: 8px;
    transition: all 0.3s ease;
}

.btn-edit:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(23, 162, 184, 0.3);
}

.btn-delete {
    background: linear-gradient(135deg, #dc3545 0%, #c82333 100%);
    color: white;
    border: none;
    padding: 6px 12px;
    border-radius: 8px;
    transition: all 0.3s ease;
}

.btn-delete:hover {
    transform: translateY(-2px);
    box-shadow: 0 4px 12px rgba(220, 53, 69, 0.3);
}

.no-results {
    padding: 3rem;
    text-align: center;
}

.alert-custom {
    background: linear-gradient(135deg, var(--light-blue) 0%, #ffffff 100%);
    border: 2px solid var(--border-blue);
    border-radius: 15px;
    padding: 2rem;
    color: var(--text-blue);
}

.custom-modal .modal-content {
    border: none;
    border-radius: 15px;
    box-shadow: 0 10px 30px rgba(44, 90, 160, 0.3);
}

.custom-modal-header {
    background: linear-gradient(135deg, var(--primary-blue) 0%, var(--secondary-blue) 100%);
    color: white;
    border-radius: 15px 15px 0 0;
    border-bottom: none;
}

.custom-close {
    filter: invert(1);
}

/* Responsive */
@media (max-width: 768px) {
    .main-title {
        font-size: 1.8rem;
    }
    
    .header-section {
        padding: 1.5rem;
    }
    
    .filters-section {
        padding: 1rem;
    }
    
    .action-buttons {
        flex-direction: column;
    }
}
</style>