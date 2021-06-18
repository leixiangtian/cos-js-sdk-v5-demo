<template>
  <div id="app">
    <el-upload
      class="upload-demo"
      action="http://127.0.0.1:7001/upload"
      :data="{appId:100}"
      :on-success="handleSuccess"
      multiple
      :limit="1"
      :file-list="fileList">
      <el-button size="small" type="primary">node端上传</el-button>
    </el-upload>
    <el-upload
      class="upload-demo"
      action=""
      :data="{appId:100}"
      :http-request="upload"
      :on-success="handleSuccess2"
      multiple
      :limit="1"
      :file-list="fileList2">
      <el-button size="small" type="primary">客户端上传</el-button>
    </el-upload>
    <input id="file-selector" type="file" @change="tirggerFile($event)">
  </div>
</template>

<script>
import COS from 'cos-js-sdk-v5';
const config = {
  SecretId: 'AKID0pOhjVzC8nCKBl58uQgeLlumMfhSw95k',
  SecretKey: 'QZR2MPAgwwf7JFuIPGq9BwQcXCZhY8RT',
  Bucket: 'test-1252615953',
  Region: 'ap-shenzhen-fsi',
  path: 'manualUploadApks/test',
};

const cos = new COS({
   SecretId: config.SecretId,
   SecretKey: config.SecretKey,
});
export default {
  name: 'App',
  data(){
    return {
      fileList: [],
      fileList2: [],
    }
  },
  methods: {
    handleSuccess(response, file, fileList){
      console.log(response)
      console.log(file)
      console.log(fileList)
    },
    handleSuccess2(response, file, fileList){
      console.log(response)
      console.log(file)
      console.log(fileList)
    },
    upload(file){
      console.log(file);
      const fileInfo = file.file;
      // const fileSize = parseInt(fileInfo.size / 1024 / 1024); // 单位mb
      const appId = file.data.appId;
      // if(fileSize > 50) {
      //   // 分片上传文件
      //   cos.sliceUploadFile({
      //       Bucket: config.Bucket,
      //       Region: config.Region,
      //       Key: `${config.path}/${appId}/${fileInfo.name}`,
      //       Body: fileInfo,
      //       onHashProgress: function (progressData) {
      //           console.log('校验中', JSON.stringify(progressData));
      //       },
      //       onProgress: function (progressData) {
      //           console.log('上传中', JSON.stringify(progressData));
      //       },
      //   }, function (err, data) {
      //       console.log(err, data);
      //   });
      // } else {
      //   // 单片上传文件
      //   cos.putObject({
      //       Bucket: config.Bucket,
      //       Region: config.Region,
      //       Key: `${config.path}/${appId}/${fileInfo.name}`,
      //       Body: fileInfo,
      //       onHashProgress: function (progressData) {
      //           console.log('校验中', JSON.stringify(progressData));
      //       },
      //       onProgress: function (progressData) {
      //           console.log('上传中', JSON.stringify(progressData));
      //       },
      //   }, function (err, data) {
      //       console.log(err, data);
      //   });
      // }
      cos.uploadFile({
            Bucket: config.Bucket,
            Region: config.Region,
            Key: `${config.path}/${appId}/${fileInfo.name}`,
            Body: fileInfo,
            SliceSize: 1024 * 1024 * 50,
            onHashProgress: function (progressData) {
                console.log('校验中', JSON.stringify(progressData));
            },
            onProgress: function (progressData) {
                console.log('上传中', JSON.stringify(progressData));
            },
        }, function (err, data) {
            console.log(err, data);
        });
    },
    tirggerFile(event){
      let file = event.target.files[0]; // (利用console.log输出看file文件对象)
    if (!file) return;

      // 分片上传文件
      cos.sliceUploadFile({
          Bucket: config.Bucket,
          Region: config.Region,
          Key: file.name,
          Body: file,
          onHashProgress: function (progressData) {
              console.log('校验中', JSON.stringify(progressData));
          },
          onProgress: function (progressData) {
              console.log('上传中', JSON.stringify(progressData));
          },
      }, function (err, data) {
          console.log(err, data);
      });
    }
  },
  mounted() {
    console.log(config.SecretId)
    console.log(cos)
  },
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>
