<template>
    <h1>Upload Form</h1>
        <div>
            <form id="uploadForm" name="uploadForm" @submit.prevent="uploadPhoto" >
                <div id = "message">
                    <p class="alert alert-success" v-if="message === 'success'" >{{success}}</p>
                    <ul class="alert alert-danger" v-if="message === 'error'" >
                        <li v-for="errors in errors" > {{errors}}</li>
                    </ul> 
                </div>
                <div class="form-group">
                    <label for="description">Description</label>
                    <textarea name="description" class="form-control"></textarea>
                </div>
                <div class="form-group">
                    <label for="photo">Photo</label>
                    <input name="photo" class="form-control-file" type="file">
                </div><br>
                <button type=submit class="btn btn-primary">Submit</button>
            </form>
        </div>    
</template>

<script lang="ts">

    export default {
        data(){
           return{
               csrf_token: '',
               message: '',
               error: [],
               success:[]
           };
        },

        methods: {
            uploadPhoto(){
                let uploadForm = document.getElementById('uploadForm');
                let form_data = new FormData(uploadForm);
                console.log(form_data)
                let self = this;
                fetch("/api/upload", {
                    method: 'POST',
                    body: form_data,
                    headers: {
                        'X-CSRFToken': this.csrf_token
                    },
                    credentials: 'same-origin'
                })

                .then(function (response) {
                    return response.json();
                })
                .then(function (jsonResponse) {
                // display a success message
                if ('data' in jsonResponse ){
                    self.success = jsonResponse.data.message;
                    self.message = 'success';
                    console.log(jsonResponse)
                } else if ('errormessage' in jsonResponse ){
                    console.log(jsonResponse)
                    self.errors = jsonResponse.errormessage.errors;
                    self.message = 'error';
                }
            })
                .catch(function (error) {
                    console.log(error);
                });
            },
            getCsrfToken() {
                let self = this;
                fetch('/api/csrf-token')

                .then((response) => response.json())

                .then((data) => {
                    console.log(data);
                    self.csrf_token = data.csrf_token;
                })
            }
        },
        created() {
            this.getCsrfToken();
            },
    }
</script>

