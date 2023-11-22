<template>
    <div class="main">
        <div class="upload_box">
            <h2>
                Upload File Here
            </h2>

            <!-- Drop over and input box div -->
            <div v-if="!disableInput"
                :class="[ dragging ? 'draggingFile' : '' ,'upload_input']" 
                @dragenter.prevent="dragging = true" 
                @dragleave.prevent="dragging = false" 
                @dragover.prevent="dragging = true"
                @drop.prevent="uploadFile"
            >
                <img src="../assets/upload-icon.png" class="file_icon" />

                <div class="file_input_text">
                    <p>
                        Drag & Drop file here
                    </p>
                    <p> or
                        <input type="file" @input="uploadFile" hidden ref="fileInput"  /> 
                        <button @click="$refs.fileInput.click" type="input"  @click.prevent="" class="file_input_btn"> 
                            Upload 
                        </button>  
                        from device
                    </p>
                </div>
            </div>
            <div class="upload_input" v-else>
                <p class="uploading_text"> Uploding File</p>

            </div>
            <!-- Progress bar to show upload progress -->
            <progress :value="fileProgress" max="100" class="uploadProgress"></progress>
            <p class="uploadTrackMessage">{{ uploadMessage }}</p>
        </div>
    </div>
</template>
<script setup>
import {ref} from "vue";

// reactive data state variables
const dragging = ref(false);
const uploadMessage = ref("No File Uploaded");
const fileProgress = ref(0);
const disableInput = ref(false);

/**
 * Function to update the progeress bar status
 * 
 * @param {number} value - the amount of data progressed
 */
function updateProgressBar(value) {
  const percent = value * 100;
  fileProgress.value = Math.round(percent);
}

/**
 * Function to upload file
 * 
 * @param {Event} e - the html event of file drag or input
 */
function uploadFile(e) {

    // modify data values
    dragging.value = false;
    disableInput.value = true;

    // retrieve file from event
    const file = e?.target?.files ?  e?.target?.files[0]:  e?.dataTransfer?.files[0];
    
    // USING THIS FREE API
    const url = 'https://httpbin.org/post';

    const method = "post";

    // USING XHR BECUASE IN FETCH API WE DON"T GET ANY TRACKING PROGRESS
    const xhr = new XMLHttpRequest();

    // update the progress message and progress bar
    xhr.upload.addEventListener('progress', event => {
        uploadMessage.value = `⏳ Uploaded ${event.loaded} bytes of ${event.total}`;
        updateProgressBar(event.loaded / event.total);
    });


    // update progress message when file uploaded
    xhr.addEventListener('loadend', () => {
        if (xhr.status === 200) {
            uploadMessage.value = '✅ Success';

        } else {
            uploadMessage.value = '❌ Error';
        }

        updateProgressBar(0);
        disableInput.value = false;
    });

    xhr.open(method, url);
    xhr.send(file);

}

</script>
<style scoped>

.uploading_text {
    color: black;

    font-size: x-large;
    text-align: center
}

.main {
    width: 100%;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
}
.upload_box {
    height: 450px;
    width: 400px;
    background-color: white;
    border-radius: 10%;
    padding: 20px;
    box-shadow: 10px 5px 5px blue;
}

h2 {
    text-align: center;
    color: blue;
}

.upload_input {
    height: 300px;
    border: 1px red dotted;
    width: 300px;
    margin: 0 auto;
    border-radius: 5%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

.file_icon {
    width: 100px;
    height: 100px;
}
.file_input_btn {
    background: none;
    border: none;
    color: blue;
    font-weight: bolder;
    font-size: larger;
    cursor: pointer;
}

.file_input_text {
    color: black;
    font-size: large;
    font-weight: bold;
    text-align: center;
}
.uploadTrackMessage {
    color: black;
    text-align: center;
    margin: 10px auto;
    font-size: small;
    font-weight: bold;
}

.uploadProgress {
    width: 100%;
    padding: 20px;
} 
.draggingFile {
    background-color: gray;
}

@media (max-width: 600px) {
    
.upload_box {
    height: 400px;
    width: 350px;
    border-radius: 5%;
    padding: 10px;
    box-shadow: 7px 3px 3px blue;
}
    
.upload_input {
    height: 250px;
    border: 1px red dotted;
    width: 250px;
    margin: 0 auto;
    border-radius: 5%;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
}

h2 {
    font-size: medium;
}

.file_icon {
    width: 50px;
    height: 50px;
}

.file_input_btn {
    font-size: large;
    cursor: pointer;
}

.file_input_text {
    font-size: medium;
}
}

</style>