<template>
    <div class="container mt-4">
        <h4>Editar Contacto</h4>

        <form @submit.prevent="emitirDatosEditados" novalidate>
            <div class="mb-3">
                <label class="form-label">Nombre *</label>
                <input v-model="editado.name" type="text" class="form-control" required />
            </div>

            <div class="mb-3">
                <label class="form-label">Email *</label>
                <input v-model="editado.email" type="email" class="form-control" required />
            </div>

            <div class="mb-3">
                <label class="form-label">Dirección</label>
                <input v-model="editado.address" type="text" class="form-control" />
            </div>

            <div class="mb-3">
                <label class="form-label">Teléfono</label>
                <input v-model="editado.phone" type="tel" class="form-control" />
            </div>

            <div class="mb-3">
                <label class="form-label">País</label>
                <select v-model="editado.country" class="form-select">
                    <option value="">Seleccione un país</option>
                    <option value="USA">Estados Unidos</option>
                    <option value="Canada">Canadá</option>
                    <option value="UK">Reino Unido</option>
                    <option value="Australia">Australia</option>
                    <option value="Mexico">México</option>
                    <option value="Spain">España</option>
                    <option value="France">Francia</option>
                    <option value="Germany">Alemania</option>
                    <option value="Brazil">Brasil</option>
                    <option value="Argentina">Argentina</option>
                    <option value="Chile">Chile</option>
                    <option value="Colombia">Colombia</option>
                    <option value="Peru">Perú</option>
                    <option value="Bolivia">Bolivia</option>
                </select>
            </div>

            <div class="mb-3">
                <label class="form-label">Ciudad</label>
                <input v-model="editado.city" type="text" class="form-control" />
            </div>

            <div class="d-flex gap-2">
                <button type="submit" class="btn btn-success">Guardar Cambios</button>
                <button type="button" class="btn btn-secondary" @click="cancelar">Cancelar</button>
            </div>
        </form>
    </div>
</template>

<script>
export default {
    name: 'ContactoEditor',
    props: {
        contacto: {
            type: Object,
            required: true
        }
    },
    data() {
        return {
            // Copia del objeto original para evitar modificarlo directamente
            editado: { ...this.contacto }
        }
    },
    watch: {
        // Observar cambios en la prop contacto para actualizar editado
        contacto: {
            handler(newVal) {
                if (newVal) {
                    this.editado = { ...newVal };
                }
            },
            deep: true,
            immediate: true
        }
    },
    methods: {
        emitirDatosEditados() {
            // Validaciones básicas
            if (!this.editado.name || this.editado.name.trim() === '') {
                alert('El nombre es obligatorio');
                return;
            }
            
            if (!this.editado.email || this.editado.email.trim() === '') {
                alert('El email es obligatorio');
                return;
            }
            
            // Validar formato de email básico
            const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
            if (!emailRegex.test(this.editado.email)) {
                alert('Por favor ingrese un email válido');
                return;
            }

            // Emitimos un evento con los datos modificados
            this.$emit('update', this.editado);
        },
        
        cancelar() {
            this.$emit('cancelar');
        }
    }
}
</script>

<style scoped>
.container {
    max-width: 600px;
}

.form-label {
    margin-bottom: 0.5rem;
    font-weight: 500;
}

.form-control, .form-select {
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
    border-radius: 0.25rem;
    transition: border-color 0.15s ease-in-out, box-shadow 0.15s ease-in-out;
}

.form-control:focus, .form-select:focus {
    border-color: #86b7fe;
    outline: 0;
    box-shadow: 0 0 0 0.25rem rgba(13, 110, 253, 0.25);
}

.mb-3 {
    margin-bottom: 1rem;
}

.btn {
    padding: 0.375rem 0.75rem;
    margin: 0.125rem;
    border: 1px solid transparent;
    border-radius: 0.25rem;
    font-size: 1rem;
    text-decoration: none;
    display: inline-block;
    cursor: pointer;
    transition: all 0.15s ease-in-out;
}

.btn-success {
    background-color: #198754;
    border-color: #198754;
    color: white;
}

.btn-success:hover {
    background-color: #157347;
    border-color: #146c43;
}

.btn-secondary {
    background-color: #6c757d;
    border-color: #6c757d;
    color: white;
}

.btn-secondary:hover {
    background-color: #5c636a;
    border-color: #565e64;
}

.d-flex {
    display: flex;
}

.gap-2 {
    gap: 0.5rem;
}
</style>