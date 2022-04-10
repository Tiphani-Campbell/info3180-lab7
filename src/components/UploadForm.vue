<template>
    <div class="upload-message">
        <div v-if="flag=='true'" class="alert alert-danger" role="alert"> 
            <ul>
                <li v-for="error in errors"> {{ error }}</li>
            </ul>
        </div>
        <div v-if="flag=='false'" class="alert alert-success" role="alert">
            {{message.message}} 
        </div> 
    </div>

   <form  @submit.prevent="uploadPhoto" id="uploadForm" method = "POST" enctype="multipart/form-data"> 
        <div class="form-group">
                <label for="description">Description</label>
                <textarea class="form-control" id="description" name="description"></textarea>
        </div>

        <div class="form-group">
            <label for ="photo">Photo</label>
            <input class = "form-control" id="photo" name="photo" type="file">
        </div> 
        <button type="submit" id="upButton" class="btn btn-primary">Upload</button>
    </form>
</template>

<script>
export default{
    data(){
        return{
            csrf_token: '',
            message: '',
            errors: [],
            flag: ''
        };
    },
    methods: {
       uploadPhoto(){
           let uploadForm = document.getElementById('uploadForm');
           let form_data = new FormData(uploadForm);
           let self = this;
           fetch("/api/upload",{
               method: 'POST',
               body: form_data,
               headers:{
                   'X-CSRFToken': this.csrf_token
               }
           })
           .then(function(response){
               return response.json();
           })
           .then(function(data){
               //display a success message
               self.message = data;
               if (self.message.hasOwnProperty('error')){
                   self.errors = self.message.error;
                   self.flag = 'true';
               }else{
                   self.flag = 'false';
               }
               
           })
           .catch(function(error){
               console.log(error);
           });
       }, 
       getCsrfToken(){
           let self = this;
           fetch("/api/csrf-token")
           .then((response) => response.json())
           .then((data)=>{
               console.log(data);
               self.csrf_token = data.csrf_token;
           })
       }
       
    },
    created(){
           this.getCsrfToken();
       } 
    
       
}    
</script>

<style>
template{
    margin: 10%;
}

form{
    margin: 5%;
}
.form-group{
    padding-bottom: 30px;
}
</style>