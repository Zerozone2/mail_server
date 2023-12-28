<template>
    <div class="attachmentsContainer" >
      
      <div class="uploadContainer">
        <label for="file-upload" class="upload-btn">Add Attachment </label>
        <input id="file-upload" type="file" class="upload-input" @change="handleFileUpload" multiple>
      </div>
      
      <div class="frame-wrapper">
        <br>
        <label for="attachments">Choode Attachment to View  </label>
        <select @change="changeCurrentFile" id="fileDropdown">
            <option value="">None</option>
        </select>
        <button class="libraryCrlBtn" @click="deleteFile()" v-if="currentFile.name">Delete</button>
        <button class="libraryCrlBtn" @click="showFileFn()" v-if="currentFile.name">Show File</button>
        <button class="libraryCrlBtn" @click="hidleFile()" v-show="showFile">Hide File</button>
            
        <div v-if="this.showFile" class="frame-container">
          <iframe :src="currentFile.url"></iframe>
        </div>
      </div>
    </div>
</template>
<option value="file">{{ file.name }}</option>

<script>

export default {
    name: 'Attachments',
    data() {
        return {
            files: [],
            showFile: false,
            currentFile: {
              name: "",
              type: "",
              url: ""
            }
        }
    },
    methods: {
        handleFileUpload(event) {

          const selectedFileList = event.target.files;
          const fileDropdown = document.getElementById('fileDropdown');

          for (let i = 0; i < selectedFileList.length; i++) {
            const file = selectedFileList[i];
            
            const newFile = {
                name: file.name,
                type: file.type,
                url: URL.createObjectURL(file),
            }
            console.log(newFile)
            this.files.push(newFile);

            const option = document.createElement('option');
            option.value = newFile["name"];
            option.textContent = newFile["name"];
            fileDropdown.appendChild(option);
          }
        },
        changeCurrentFile(){
          const fileDropdown = document.getElementById('fileDropdown')
          const selectedFile = fileDropdown.value;

          if (selectedFile) {
            this.files.forEach(file => {
              if( file["name"] == selectedFile ){
                this.currentFile = file
              }
            })

          } else {
            this.showFile = false
            this.currentFile = {
              name: "",
              type: "",
              url: ""
            }
          }
        },
        showFileFn() {
          this.showFile = true
        },
        hidleFile() {
          this.showFile = false
        },
        deleteFile() {
          URL.revokeObjectURL(this.currentFile["url"]); 
          this.files = this.files.filter( item => item != this.currentFile)
          const dropdown = document.getElementById('fileDropdown');
          const selectedIndex = dropdown.selectedIndex;

          if (selectedIndex !== -1 && selectedIndex !== 0) {
            dropdown.remove(selectedIndex);
            this.currentFile = {
              name: "",
              type: "",
              url: ""
            }
            this.showFile = false
          } else {
            alert('Please select a file to remove.');
          }
        },
        async sendMail() {

            const file = {
              name: this.currentFile["name"],
              type: this.currentFile["type"],
              url: this.currentFile["url"],
            };

            if (file) {
                const formData = new FormData();
                formData.append('file', file);

                await fetch('http://localhost:8080/mail/send/', {
                    method: 'POST',
                    body: JSON.stringify(file)
                })
            }

        },
        async receiveMail() {

            const result = await fetch('http://localhost:8080/mail/receive/')
            
            const mail = await result.json();

        },
    }
}

</script>

<style>
.attachmentsContainer {
  width: 100%;
  text-align: center;
  padding: 10px;
  height: 100%;
  box-sizing: border-box;
}

.frame-wrapper {
  width: 100%;
  margin: 5px;
}

.frame-wrapper label {
  padding-bottom: 10px;
  padding-top: 10px;
}

#fileDropdown {
  text-overflow: ellipsis; 
  border: 0px solid;
  max-width: 150px;
}

#fileDropdown:focus{
    outline: none;
    border: 0px solid;
    

}

.frame-container iframe,.frame-container img,.frame-container video {
  width: 420px;
  height: 100%;
  max-width: 100%;
  max-height: 100%;
  /* object-fit: contain; */
}

.libraryCrlBtn{
  background-color: lightgrey;
  border: none;
  margin: 5px;
}

.libraryCrlBtn:hover{
  background-color: grey
}
.frame-container {
  width: 100%;
  height: 75vh;
}
.upload-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  margin-bottom: 10px;
}
.upload-input {
  margin-bottom: 10px;
  display: none;
}
.upload-btn {
  padding: 8px 16px;
  border: none;
  background-color: lightgray;
  cursor: pointer;
}
.upload-btn:hover {
  background-color: grey;
}
</style>