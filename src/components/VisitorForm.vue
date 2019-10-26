<template>
    <div id="visitor-form">
        <form @submit.prevent="handleSubmit">
            <label>Visitor name</label>
            <input
                    ref="first"
                    type="text"
                    :class="{ 'has-error': submitting && invalidName }"
                    v-model="visitor.name"
                    @focus="clearStatus"
                    @keypress="clearStatus"
            />
            <label>Visitor address</label>
            <input
                    type="text"
                    :class="{ 'has-error': submitting && invalidAddress }"
                    v-model="visitor.address"
                    @focus="clearStatus"
            />
            <label>Visitor Phone Number</label>
            <input
                    type="number"
                    :class="{ 'has-error': submitting && invalidPhone }"
                    v-model="visitor.phone"
                    @focus="clearStatus"
            />

            <p v-if="error && submitting" class="error-message">
                ❗ Please fill out all required fields
            </p>
            <p v-if="success" class="success-message">
                ✅ Visitor successfully added
            </p>
            <button>Add Visitor</button>
        </form>
    </div>
</template>

<script>
    var dateFormat = require('dateformat');
    export default {
        name: 'visitor-form',
        data() {
            return {
                submitting: false,
                error: false,
                success: false,
                visitor: {
                    name: '',
                    address: '',
                    phone: '',
                    visit_at: ''
                }
            }
        },
        methods: {
            handleSubmit() {
                this.submitting = true
                this.clearStatus()
                if (this.invalidAddress || this.invalidName || this.invalidPhone) {
                    this.error = true
                    return
                }
                let currentDate = new Date();
                this.visitor.visit_at = dateFormat(currentDate, "dd-mm-yyyy HH:MM:ss");
                this.$emit('add:visitor', this.visitor)
                this.$refs.first.focus()

                this.visitor = {
                    name: '',
                    address: '',
                    phone: '',
                    visit_at: ''
                }
                this.error = false
                this.success = true
                this.submitting = false
            },
            clearStatus() {
                this.success = false
                this.error = false
            }
        },
        computed: {
            invalidName() {
                return this.visitor.name === ''
            },
            invalidAddress() {
                return this.visitor.address === ''
            },
            invalidPhone() {
                return this.visitor.phone === '' || !this.visitor.phone.match(/^\d{9,15}$/)
            },
        }
    }
</script>

<style scoped>
    form {
        margin-bottom: 2rem;
    }

    [class*='-message'] {
        font-weight: 500;
    }

    .error-message {
        color: #d33c40;
    }

    .success-message {
        color: #32a95d;
    }
</style>
