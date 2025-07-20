<template>
    <div class="contacts-container">
        <div class="container">
            <!-- Header con título y botón -->
            <div class="header-section">
                <h1 class="main-title">📞 Gestión de Contactos</h1>
                <button type="button" class="btn-add-contact" @click="goToNew()">
                    + Nuevo Contacto
                </button>
            </div>
            
            <!-- Sección de filtros -->
            <div class="filters-section">
                <div class="filters-row">
                    <div class="filter-group">
                        <span class="filter-icon">🔍</span>
                        <input type="search" v-model="nombreABuscar" class="filter-input" 
                               placeholder="Buscar por nombre...">
                    </div>
                    <div class="filter-group">
                        <span class="filter-icon">📧</span>
                        <input type="search" v-model="emailABuscar" class="filter-input" 
                               placeholder="Buscar por email...">
                    </div>
                    <div class="filter-group">
                        <span class="filter-icon">🌍</span>
                        <select v-model="paisSeleccionado" class="filter-select">
                            <option value="">Todos los países</option>
                            <option v-for="pais in paisesUnicos" :key="pais" :value="pais">{{ pais }}</option>
                        </select>
                    </div>
                </div>
                
                <div class="filters-row-2">
                    <div class="filter-group">
                        <span class="filter-icon">🏙️</span>
                        <input type="search" v-model="ciudadABuscar" class="filter-input" 
                               placeholder="Buscar por ciudad...">
                    </div>
                    <div class="clear-filters-container">
                        <button type="button" class="btn-clear-filters" @click="limpiarFiltros">
                            🗑️ Limpiar Filtros
                        </button>
                    </div>
                </div>
            </div>

            <!-- Contador de resultados -->
            <div class="results-counter">
                <span class="counter-badge">
                    Mostrando {{ getContactos.length }} de {{ contactos.length }} contactos
                </span>
            </div>

            <!-- Tabla de contactos -->
            <div class="table-container">
                <table class="contacts-table">
                    <thead class="table-header">
                        <tr>
                            <th>ID</th>
                            <th>Nombre</th>
                            <th>Email</th>
                            <th>Teléfono</th>
                            <th>País</th>
                            <th>Ciudad</th>
                            <th>Acciones</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(contacto, index) in getContactos" :key="contacto.id" class="table-row">
                            <td class="id-cell">{{ contacto.id }}</td>
                            <td class="name-cell">{{ contacto.name }}</td>
                            <td class="email-cell">{{ contacto.email }}</td>
                            <td>{{ contacto.phone || '-' }}</td>
                            <td>
                                <span v-if="contacto.country" class="country-badge">{{ contacto.country }}</span>
                                <span v-else>-</span>
                            </td>
                            <td>{{ contacto.city || '-' }}</td>
                            <td>
                                <div class="action-buttons">
                                    <button type="button" class="btn-edit" 
                                            @click="abrirModal(index)" title="Editar">
                                        ✏️
                                    </button>
                                    <button type="button" class="btn-delete" 
                                            @click="eliminar(index)" title="Eliminar">
                                        🗑️
                                    </button>
                                </div>
                            </td>
                        </tr>
                    </tbody>
                </table>
                
                <div v-if="getContactos.length === 0" class="no-results">
                    <div class="no-results-card">
                        <h5>📋 No se encontraron contactos</h5>
                        <p>{{ contactos.length === 0 ? 'No hay contactos registrados.' : 'No hay contactos que coincidan con los filtros aplicados.' }}</p>
                    </div>
                </div>
            </div>
        </div>

        <!-- Modal -->
        <div v-if="showModal" class="modal-overlay" @click="cerrarModal">
            <div class="modal-content" @click.stop>
                <div class="modal-header">
                    <h5 class="modal-title">
                        {{ modalMode === 'editar' ? '✏️ Editar Contacto' : '➕ Nuevo Contacto' }}
                    </h5>
                    <button type="button" class="modal-close" @click="cerrarModal">×</button>
                </div>
                <div class="modal-body">
                    <ContactoEditor v-if="modalMode === 'editar' && contactoSeleccionado" 
                                    :contacto="contactoSeleccionado" 
                                    @update="guardarEdicion"
                                    @cancelar="cerrarModal" />
                    <NuevoContacto v-if="modalMode === 'crear'" 
                                   @created="agregarNuevo($event)" />
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import ContactoEditor from './ContactoEditor.vue';
import NuevoContacto from './NuevoContacto.vue';

export default {
    name: 'ContactsView',
    data() {
        return {
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
            contactoSeleccionado: null,
            indiceSeleccionado: 0,
            nombreABuscar: "",
            emailABuscar: "",
            paisSeleccionado: "",
            ciudadABuscar: "",
            modalMode: "crear",
            showModal: false
        }
    },
    components: {
        ContactoEditor,
        NuevoContacto,
    },
    methods: {
        goToNew() {
            this.modalMode = "crear";
            this.showModal = true;
        },
        abrirModal(index) {
            this.indiceSeleccionado = index;
            this.modalMode = "editar";
            this.contactoSeleccionado = { ...this.getContactos[index] };
            this.showModal = true;
        },
        cerrarModal() {
            this.showModal = false;
            this.contactoSeleccionado = null;
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
            
            if (this.nombreABuscar !== "") {
                result = result.filter((contacto) => {
                    const nombre = contacto.name || "";
                    return nombre.toLowerCase().includes(this.nombreABuscar.toLowerCase());
                });
            }
            
            if (this.emailABuscar !== "") {
                result = result.filter((contacto) => {
                    const email = contacto.email || "";
                    return email.toLowerCase().includes(this.emailABuscar.toLowerCase());
                });
            }

            if (this.paisSeleccionado !== "") {
                result = result.filter((contacto) => {
                    return contacto.country === this.paisSeleccionado;
                });
            }

            if (this.ciudadABuscar !== "") {
                result = result.filter((contacto) => {
                    const ciudad = contacto.city || "";
                    return ciudad.toLowerCase().includes(this.ciudadABuscar.toLowerCase());
                });
            }
            
            return result;
        }
    }
}
</script>

<style scoped>
* {
    box-sizing: border-box;
}

/* Contenedor principal */
.contacts-container {
    background: #f8f9fa;
    min-height: 100vh;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
}

.container {
    max-width: 1200px;
    margin: 0 auto;
    padding: 20px;
}

/* Header */
.header-section {
    background: linear-gradient(135deg, #4a90e2 0%, #357abd 100%);
    color: white;
    padding: 30px;
    border-radius: 12px;
    margin-bottom: 25px;
    box-shadow: 0 4px 15px rgba(74, 144, 226, 0.3);
    text-align: center;
}

.main-title {
    font-size: 2.2rem;
    font-weight: 700;
    margin: 0 0 20px 0;
    text-shadow: 0 2px 4px rgba(0,0,0,0.2);
}

.btn-add-contact {
    background: white;
    color: #4a90e2;
    border: none;
    padding: 12px 24px;
    border-radius: 25px;
    font-weight: 600;
    font-size: 1rem;
    cursor: pointer;
    transition: all 0.3s ease;
    box-shadow: 0 2px 10px rgba(255,255,255,0.2);
}

.btn-add-contact:hover {
    background: #f0f8ff;
    transform: translateY(-2px);
    box-shadow: 0 4px 15px rgba(255,255,255,0.3);
}

/* Filtros */
.filters-section {
    background: white;
    padding: 25px;
    border-radius: 12px;
    box-shadow: 0 2px 10px rgba(0,0,0,0.1);
    margin-bottom: 20px;
}

.filters-row {
    display: grid;
    grid-template-columns: 1fr 1fr 1fr;
    gap: 20px;
    margin-bottom: 20px;
}

.filters-row-2 {
    display: grid;
    grid-template-columns: 1fr auto;
    gap: 20px;
    align-items: end;
}

.filter-group {
    position: relative;
}

.filter-icon {
    position: absolute;
    left: 15px;
    top: 50%;
    transform: translateY(-50%);
    z-index: 2;
    font-size: 1.1rem;
}

.filter-input, .filter-select {
    width: 100%;
    padding: 12px 15px 12px 45px;
    border: 2px solid #e9ecef;
    border-radius: 8px;
    font-size: 1rem;
    background: white;
    transition: all 0.3s ease;
}

.filter-input:focus, .filter-select:focus {
    outline: none;
    border-color: #4a90e2;
    box-shadow: 0 0 0 3px rgba(74, 144, 226, 0.1);
}

.btn-clear-filters {
    background: #dc3545;
    color: white;
    border: none;
    padding: 12px 20px;
    border-radius: 8px;
    font-weight: 500;
    cursor: pointer;
    transition: all 0.3s ease;
    white-space: nowrap;
}

.btn-clear-filters:hover {
    background: #c82333;
    transform: translateY(-1px);
}

/* Contador */
.results-counter {
    text-align: center;
    margin-bottom: 20px;
}

.counter-badge {
    background: #4a90e2;
    color: white;
    padding: 8px 20px;
    border-radius: 20px;
    font-size: 0.9rem;
    font-weight: 500;
    display: inline-block;
}

/* Tabla */
.table-container {
    background: white;
    border-radius: 12px;
    overflow: hidden;
    box-shadow: 0 2px 15px rgba(0,0,0,0.1);
}

.contacts-table {
    width: 100%;
    border-collapse: collapse;
    background: white;
}

.table-header {
    background: linear-gradient(135deg, #4a90e2 0%, #357abd 100%);
}

.table-header th {
    color: white;
    padding: 15px 10px;
    font-weight: 600;
    text-align: center;
    border: none;
    font-size: 0.95rem;
}

.table-row {
    transition: all 0.3s ease;
    border-bottom: 1px solid #e9ecef;
}

.table-row:hover {
    background: #f8f9fa;
}

.table-row:last-child {
    border-bottom: none;
}

.contacts-table td {
    padding: 15px 10px;
    text-align: center;
    vertical-align: middle;
    font-size: 0.9rem;
}

.id-cell {
    background: #e3f2fd;
    color: #1976d2;
    font-weight: bold;
    width: 60px;
}

.name-cell {
    font-weight: 600;
    color: #333;
}

.email-cell {
    color: #4a90e2;
    font-style: italic;
}

.country-badge {
    background: #4a90e2;
    color: white;
    padding: 4px 12px;
    border-radius: 15px;
    font-size: 0.8rem;
    font-weight: 500;
    display: inline-block;
}

.action-buttons {
    display: flex;
    gap: 8px;
    justify-content: center;
}

.btn-edit {
    background: #17a2b8;
    color: white;
    border: none;
    padding: 8px 12px;
    border-radius: 6px;
    cursor: pointer;
    transition: all 0.3s ease;
    font-size: 0.9rem;
}

.btn-edit:hover {
    background: #138496;
    transform: translateY(-1px);
}

.btn-delete {
    background: #dc3545;
    color: white;
    border: none;
    padding: 8px 12px;
    border-radius: 6px;
    cursor: pointer;
    transition: all 0.3s ease;
    font-size: 0.9rem;
}

.btn-delete:hover {
    background: #c82333;
    transform: translateY(-1px);
}

/* Sin resultados */
.no-results {
    padding: 40px;
    text-align: center;
}

.no-results-card {
    background: #f8f9fa;
    border: 2px solid #e9ecef;
    border-radius: 12px;
    padding: 30px;
    color: #6c757d;
}

.no-results-card h5 {
    margin-bottom: 10px;
    color: #495057;
}

/* Modal */
.modal-overlay {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background: rgba(0, 0, 0, 0.5);
    display: flex;
    justify-content: center;
    align-items: center;
    z-index: 1000;
}

.modal-content {
    background: white;
    border-radius: 12px;
    box-shadow: 0 10px 30px rgba(0, 0, 0, 0.3);
    max-width: 600px;
    width: 90%;
    max-height: 80vh;
    overflow-y: auto;
}

.modal-header {
    background: linear-gradient(135deg, #4a90e2 0%, #357abd 100%);
    color: white;
    padding: 20px;
    border-radius: 12px 12px 0 0;
    display: flex;
    justify-content: space-between;
    align-items: center;
}

.modal-title {
    margin: 0;
    font-size: 1.3rem;
    font-weight: 600;
}

.modal-close {
    background: none;
    border: none;
    color: white;
    font-size: 2rem;
    cursor: pointer;
    padding: 0;
    width: 30px;
    height: 30px;
    display: flex;
    align-items: center;
    justify-content: center;
    border-radius: 50%;
    transition: background 0.3s ease;
}

.modal-close:hover {
    background: rgba(255, 255, 255, 0.2);
}

.modal-body {
    padding: 0;
}

/* Responsive */
@media (max-width: 768px) {
    .main-title {
        font-size: 1.8rem;
    }
    
    .filters-row {
        grid-template-columns: 1fr;
        gap: 15px;
    }
    
    .filters-row-2 {
        grid-template-columns: 1fr;
        gap: 15px;
    }
    
    .contacts-table {
        font-size: 0.8rem;
    }
    
    .contacts-table td,
    .table-header th {
        padding: 10px 5px;
    }
    
    .action-buttons {
        flex-direction: column;
        gap: 5px;
    }
    
    .modal-content {
        width: 95%;
        margin: 20px;
    }
}

@media (max-width: 480px) {
    .container {
        padding: 15px;
    }
    
    .header-section {
        padding: 20px;
    }
    
    .filters-section {
        padding: 20px;
    }
    
    /* Ocultar algunas columnas en móvil */
    .contacts-table th:nth-child(4),
    .contacts-table td:nth-child(4),
    .contacts-table th:nth-child(6),
    .contacts-table td:nth-child(6) {
        display: none;
    }
}
</style>