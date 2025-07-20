<template>
    <div class="container mt-4">
        <h4>Nuevo Contacto</h4>

        <form @submit.prevent="submit" novalidate>
            <div class="mb-3">
                <label class="form-label">Nombre *</label>
                <input v-model="contacto.name" type="text" class="form-control" 
                       :class="{'is-invalid': errors.name}" />
                <div v-if="errors.name" class="invalid-feedback">{{ errors.name }}</div>
            </div>

            <div class="mb-3">
                <label class="form-label">Email *</label>
                <input v-model="contacto.email" type="email" class="form-control" 
                       :class="{'is-invalid': errors.email}" />
                <div v-if="errors.email" class="invalid-feedback">{{ errors.email }}</div>
            </div>

            <div class="mb-3">
                <label class="form-label">Dirección</label>
                <input v-model="contacto.address" type="text" class="form-control" />
            </div>

            <div class="mb-3">
                <label class="form-label">Teléfono</label>
                <input v-model="contacto.phone" type="tel" class="form-control" 
                       :class="{'is-invalid': errors.phone}" />
                <div v-if="errors.phone" class="invalid-feedback">{{ errors.phone }}</div>
            </div>

            <div class="mb-3">
                <label class="form-label">País</label>
                <select v-model="contacto.country" class="form-select">
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
                <input v-model="contacto.city" type="text" class="form-control" />
            </div>

            <div class="d-flex gap-2">
                <button type="submit" class="btn btn-primary">Guardar Contacto</button>
                <button type="button" class="btn btn-secondary" @click="limpiarFormulario">Limpiar</button>
            </div>
        </form>
    </div>
</template>

<script>
export default {
    name: 'NuevoContacto',
    data() {
        return {
            contacto: {
                name: '',
                email: '',
                address: '',
                phone: '',
                country: '',
                city: ''
            },
            errors: {}
        }
    },
    methods: {
        validarFormulario() {
            this.errors = {};
            let isValid = true;

            // Validar nombre
            if (!this.contacto.name || this.contacto.name.trim() === '') {
                this.errors.name = 'El nombre es obligatorio';
                isValid = false;
            }

            // Validar email
            if (!this.contacto.email || this.contacto.email.trim() === '') {
                this.errors.email = 'El email es obligatorio';
                isValid = false;
            } else {
                const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
                if (!emailRegex.test(this.contacto.email)) {
                    this.errors.email = 'Por favor ingrese un email válido';
                    isValid = false;
                }
            }

            // Validar teléfono (opcional pero si se proporciona debe tener formato válido)
            if (this.contacto.phone && this.contacto.phone.trim() !== '') {
                const phoneRegex = /^[\d\s\-\+\(\)]+$/;
                if (!phoneRegex.test(this.contacto.phone)) {
                    this.errors.phone = 'Por favor ingrese un teléfono válido';
                    isValid = false;
                }
            }

            return isValid;
        },

        submit() {
            if (!this.validarFormulario()) {
                return;
            }

            // Limpiar espacios en blanco
            const contactoLimpio = {
                name: this.contacto.name.trim(),
                email: this.contacto.email.trim(),
                address: this.contacto.address.trim(),
                phone: this.contacto.phone.trim(),
                country: this.contacto.country,
                city: this.contacto.city.trim()
            };

            // Emitir evento con el nuevo contacto
            this.$emit('created', contactoLimpio);
            
            // Limpiar formulario
            this.limpiarFormulario();
        },

        limpiarFormulario() {
            this.contacto = {
                name: '',
                email: '',
                address: '',
                phone: '',
                country: '',
                city: ''
            };
            this.errors = {};
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

.form-control.is-invalid, .form-select.is-invalid {
    border-color: #dc3545;
}

.invalid-feedback {
    width: 100%;
    margin-top: 0.25rem;
    font-size: 0.875em;
    color: #dc3545;
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

.btn-primary {
    background-color: #0d6efd;
    border-color: #0d6efd;
    color: white;
}

.btn-primary:hover {
    background-color: #0b5ed7;
    border-color: #0a58ca;
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