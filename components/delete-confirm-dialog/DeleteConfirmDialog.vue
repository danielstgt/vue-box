<template>
    <div class="inline-block" @click="handle">
        <slot></slot>
    </div>
</template>

<script>
export default {
    props: ['action', 'title', 'body', 'success', 'successLocation'],

    data() {
        return {
            errors: {},
        }
    },

    methods: {
        handle() {
            swal({
                title: this.title,
                text: this.body,
                type: 'warning',
                showCancelButton: true,
                confirmButtonText: 'Ja, löschen',
                cancelButtonText: 'Abbrechen'
            }).then(result => {
                if (result.value) {
                    axios
                        .delete('/api' + this.action)
                        .then(response => {
                            swal({
                                type: 'success',
                                title: this.success,
                                showConfirmButton: false,
                                timer: 1500
                            }).then(result => {
                                this.successLocation ? location.href = this.successLocation : location.reload();
                            });
                        })
                        .catch(error => {
                            this.errors = error.response.data.errors;
                        });
                }
            });
        }
    }
}
</script>
