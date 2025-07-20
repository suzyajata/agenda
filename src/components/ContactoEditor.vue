<template>
    <div class="editor-container">
        <form @submit.prevent="emitirDatosEditados" novalidate>
            <div class="form-group">
                <label class="form-label">Nombre *</label>
                <input v-model="editado.name" type="text" class="form-input" required />
            </div>

            <div class="form-group">
                <label class="form-label">Email *</label>
                <input v-model="editado.email" type="email" class="form-input" required />
            </div>

            <div class="form-group">
                <label class="form-label">Dirección</label>
                <input v-model="editado.address" type="text" class="form-input" />
            </div>

            <div class="form-group">
                <label class="form-label">Teléfono</label>
                <input v-model="editado.phone" type="tel" class="form-input" />
            </div>

            <div class="form-group">
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

            <div class="form-group">
                <label class="form-label">Ciudad</label>
                <input v-model="editado.city" type="text" class="form-input" />
            </div>

            <div class="form-buttons">
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
            editado: { ...this.contacto }
        }
    },
    watch: {
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
            if (!this.editado.name || this.editado.name.trim() === '') {
                alert('El nombre es obligatorio');
                return;
            }
            
            if (!this.editado.email || this.editado.email.trim() === '') {
                alert('El email es obligatorio');
                return;
            }
            
            const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
            if (!emailRegex.test(this.editado.email)) {
                alert('Por favor ingrese un email válido');
                return;
            }

            this.$emit('update', this.editado);
        },
        
        cancelar() {
            this.$emit('cancelar');
        }
    }
}
</script>

<style scoped>
.editor-container {
    padding: 25px;
    font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
}

.form-group {
    margin-bottom: 20px;
}

.form-label {
    display: block;
    margin-bottom: 8px;
    font-weight: 600;
    color: #333;
    font-size: 0.95rem;
}

.form-input, .form-select {
    width: 100%;
    padding: 12px 15px;
    border: 2px solid #e9ecef;
    border-radius: 8px;
    font-size: 1rem;
    font-family: inherit;
    background: white;
    transition: all 0.3s ease;
}

.form-input:focus, .form-select:focus {
    outline: none;
    border-color: #4a90e2;
    box-shadow: 0 0 0 3px rgba(74, 144, 226, 0.1);
}

.form-select {
    cursor: pointer;
}

.form-buttons {
    display: flex;
    gap: 12px;
    justify-content: flex-end;
    margin-top: 30px;
    padding-top: 20px;
    border-top: 1px solid #e9ecef;
}

.btn {
    padding: 12px 24px;
    border: none;
    border-radius: 8px;
    font-size: 1rem;
    font-weight: 600;
    cursor: pointer;
    transition: all 0.3s ease;
    text-decoration: none;
    display: inline-block;
}

.btn-success {
    background: #28a745;
    color: white;
}

.btn-success:hover {
    background: #218838;
    transform: translateY(-1px);
    box-shadow: 0 4px 12px rgba(40, 167, 69, 0.3);
}

.btn-secondary {
    background: #6c757d;
    color: white;
}

.btn-secondary:hover {
    background: #5a6268;
    transform: translateY(-1px);
    box-shadow: 0 4px 12px rgba(108, 117, 125, 0.3);
}

@media (max-width: 480px) {
    .editor-container {
        padding: 20px;
    }
    
    .form-buttons {
        flex-direction: column;
    }
    
    .btn {
        width: 100%;
    }
}
</style>