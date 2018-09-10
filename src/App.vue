<template>
  <div id="app">
    <label>Files
      <input type="file" id="files" ref="files" multiple v-on:change="handleFilesUpload()" />
    </label>
    <div v-for="(file, key) in files" class="file-listing">{{ file.name }} <span class="remove-file" v-on:click="removeFile( key )">Remove</span></div>
    <button v-on:click="addFiles()">Add Files</button>
  </div>
</template>

<script>
  //1. 엑셀파일을 사용자에게 받는다
  //2. 받은 엑셀파일을 json으로 변환한다.
  //3. json에서 내가 원하는 포멧으로 변환
  import Vue from 'vue';
  import XLSX from 'xlsx';

  export default {
    data() {
      return {
        files: []
      }
    },
    methods: {
      addFiles() {
        //$refs는 컴포넌트, DOM에 접근할 수 있게해줌 
        //HACK!! click()가 어디서 오는거?
        this.$refs.files.click();
      },

      handleFilesUpload() {
        // console.log(this.$refs.files); //input tag가 딸려옴
        //console.log(this.$refs.files.files); //input tag에 담긴 파일
        let uploadedFiles = this.$refs.files.files;

        for (var i = 0; i < uploadedFiles.length; i++) {
          this.files.push(uploadedFiles[i]);
        }

        var f = uploadedFiles[0];
        var reader = new FileReader();
        reader.onload = function (e) {
          var data = e.target.result;
          data = new Uint8Array(data);
          var workbook = XLSX.read(data, {
            type: "array"
          });

          /* DO SOMETHING WITH workbook HERE */
          var first_sheet_name = workbook.SheetNames[0];
          /* Get worksheet */
          var worksheet = workbook.Sheets[first_sheet_name];

          //It will prints with header and contents ex) Name, Home...
          var json = XLSX.utils.sheet_to_json(worksheet, {
            header: 1
          });
          console.log(json);
        }
        reader.readAsArrayBuffer(f);
      },
      removeFile(key) {
        this.files.splice(key, 1);
      }
    }
  }

</script>
