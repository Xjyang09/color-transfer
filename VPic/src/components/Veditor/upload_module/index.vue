<template>
  <div class="upload" v-if="!uploaded" @change="change" @dragover="dragover" @drop="drop">
    <el-dialog title="图片导入" :visible.sync="dialogTableVisible">
      <div class="primary-pic-ct chrome-scrollbar">
        <el-row :gutter="10">
          <el-col :span="8" v-for="item in preChoiseImg">
            <img class="select-img"
                :data-width="item.width"
                :data-height="item.height"
                :src="item.src"
                :data-name="item.name"
                @click="selectImg">
          </el-col>
        </el-row>
      </div>
      <div class="upload-btn J-upload" title="选择本地图片">
        <span class="upload-icon"></span>
        <input id="file" type="file" accept="image/svg,image/gif,image/png,image/jpeg,image/bmp,image/webp" class="sr-only">
      </div>
    </el-dialog>
      <!--<div class="upload-btn J-upload" title="选择本地图片">-->


    <div class="import-area J-import" @click="dialogTableVisible = true">
      <span class="icon"></span>
      <p class="tip">拖入图片或 <span class="browse">点击导入</span></p>
    </div>
  </div>
</template>

<script>
  // import Vdialog from './dialog';

  export default {
    components: {
    // <vdialog :dialogTableVisible="dialogTableVisible"></vdialog>

      // Vdialog,
    },
    data() {
      return {
        // 预选图片弹窗状态
        dialogTableVisible: false,
        // 系统预选图片 Map
        preChoiseImg: [
          {
            width: '1600',
            height: '900',
            name: 'test-1.jpeg',
            src: './static/image/test-1.jpeg',
          },
          {
            width: '1600',
            height: '900',
            name: 'test-2.jpg',
            src: './static/image/test-2.jpg',
          },
          {
            width: '1600',
            height: '900',
            name: 'test-3.jpeg',
            src: './static/image/test-3.jpeg',
          },
          {
            width: '1600',
            height: '900',
            name: 'test-4.jpg',
            src: './static/image/test-4.jpg',
          },
          {
            width: '1600',
            height: '900',
            name: 'test-5.jpeg',
            src: './static/image/test-5.jpeg',
          },
          {
            width: '1600',
            height: '900',
            name: 'test-6.jpg',
            src: './static/image/test-6.jpg',
          },
          {
            width: '1600',
            height: '900',
            name: 'test-7.jpg',
            src: './static/image/test-7.jpg',
          },
          {
            width: '1600',
            height: '900',
            name: 'test-8.jpg',
            src: './static/image/test-8.jpg',
          },
          {
            width: '1600',
            height: '900',
            name: 'test-9.jpg',
            src: './static/image/test-9.jpg',
          },
        ],
      };
    },
    computed: {
      uploaded() {
        return this.$store.state.uploaded;
      },
    },
    methods: {
      // getFile(e) {
      //   let imgwidth = 0;
      //   let imgheight = 0;
      //   const url = URL.createObjectURL(e.target.files[0]);
      //   const imageObject = new Image();
      //   imageObject.src = url;
      //   imageObject.onload = () => {
      //     imgwidth = imageObject.width;
      //     imgheight = imageObject.height;
      //     this.$store.dispatch('setUpload');
      //     // store传递类型以及文件信息
      //     this.$store.dispatch('setImgMsg', {
      //       type: 'image/jpeg',
      //       name,
      //       url,
      //       width: +imgwidth,
      //       height: +imgheight,
      //     });
      //     console.log(this.$store.state.imgMsg);
      //   };
      //   // const imgwidth = imageObject.width;
      //   // const imgheight = imageObject.height;
      // },
      calCanvasWidthAndHeight(imgWidth, imgHeight) {
        const maxWidth = Math.round((document.body.clientWidth - 300) * 0.75);
        const maxHeight = Math.round((document.body.clientHeight - 60) * 0.75);
        let canvasWidth = 0;
        let canvasHeight = 0;
        if (imgWidth >= imgHeight) {
          const n = imgWidth / maxWidth;
          canvasWidth = Math.round(imgWidth / n);
          canvasHeight = Math.round(imgHeight / n);
        } else {
          const n = imgHeight / maxHeight;
          canvasWidth = Math.round(imgWidth / n);
          canvasHeight = Math.round(imgHeight / n);
        }
        return {
          canvasWidth,
          canvasHeight,
        };
      },
      read(file, callback = () => {}) {
        const imgReg = /^image\/\w+$/;
        const imgMaxSize = 3; // 上传图片最大体积，单位 mb
        const imgWarnSize = 0.8; // 上传图片警戒体积，单位 mb
        let reader = null;

        if (file) {
          if (imgReg.test(file.type)) {
            // fileReader读取的文件体积单位为字节 b
            const imgSize = file.size / (1024 * 1024);

            if (imgSize < imgMaxSize) {
              if (imgSize > imgWarnSize) {
                this.$notify({
                  title: '提示',
                  message: '图片体积过大 处理速度可能会下降',
                  type: 'warning',
                  duration: 4000,
                  offset: 120,
                });
              }

              reader = new FileReader(); // 实例化 Web Api FileReader

              reader.onload = () => {
                let canvas = document.createElement('canvas');
                let ctx = canvas.getContext('2d');
                let imgWidth = 0;
                let imgHeight = 0;
                const imageObject = new Image();
                imageObject.src = reader.result;
                // const imageObject = new Image();
                // imageObject.src = target;
                // // Wait until the image loads
                // imageObject.onload = () => {
                imageObject.onload = () => {
                  imgWidth = +imageObject.width;
                  imgHeight = +imageObject.height;
                  const canvasWidthHeight = this.calCanvasWidthAndHeight(imgWidth, imgHeight);
                  canvas.width = canvasWidthHeight.canvasWidth;
                  canvas.height = canvasWidthHeight.canvasHeight;
                  ctx.drawImage(imageObject, 0, 0, canvas.width, canvas.height);
                  const URL = canvas.toDataURL('image/jpeg');
                  // 上传区域置空
                  this.$store.dispatch('setUpload');
                  // store传递类型以及文件信息
                  this.$store.dispatch('setImgMsg', {
                    type: 'image/jpeg',
                    name,
                    url: URL,
                    width: canvasWidthHeight.canvasWidth,
                    height: canvasWidthHeight.canvasHeight,
                  });
                }
                //
                //
                //
                //
                // const imageObject = new Image();
                // imageObject.src = reader.result;
                // let imgWidth = 0;
                // let imgHeight = 0;
                // imageObject.onload = () => {
                //   imgWidth = +imageObject.width;
                //   imgHeight = +imageObject.height;
                //   const canvasWidthHeight = this.calCanvasWidthAndHeight(imgWidth, imgHeight);
                //   console.log(canvasWidthHeight);
                //   // 上传区域置空
                //   this.$store.dispatch('setUpload');
                //   // store传递类型以及文件信息
                //   this.$store.dispatch('setImgMsg', {
                //     type: file.type,
                //     name: file.name,
                //     url: reader.result,
                //     width: canvasWidthHeight.canvasWidth,
                //     height: canvasWidthHeight.canvasHeight,
                //   });
                // };
                callback();
              };

              reader.readAsDataURL(file);
            } else {
              this.$message({
                message: '图片体积须低于3M 请重新选择',
                type: 'warning',
              });
              callback();
            }
          } else {
            this.$message({
              message: '请选择图片文件',
              type: 'warning',
            });
            callback();
          }
        } else {
          callback();
        }
      },
      change(e) {
        const target = e.target;
        const files = target.files;

        this.read(files && files[0], () => {
          target.value = '';
        });
      },
      dragover(e) {
        e.preventDefault();
      },
      drop(e) {
        const files = e.dataTransfer.files;
        e.preventDefault();
        this.read(files && files[0]);
      },
      selectImg(e) {
        const target = e.target;
        // const url = this.getBase64Image(target);
        const name = target.dataset.name;
        const canvasWidthHeight = this.calCanvasWidthAndHeight(+target.dataset.width, +target.dataset.height);

        let canvas = document.createElement('canvas');
        canvas.width = canvasWidthHeight.canvasWidth;
        canvas.height = canvasWidthHeight.canvasHeight;
        let ctx = canvas.getContext('2d');
        // const imageObject = new Image();
        // imageObject.src = target;
        // // Wait until the image loads
        // imageObject.onload = () => {
        ctx.drawImage(target, 0, 0, canvas.width, canvas.height);
        const URL = canvas.toDataURL('image/jpeg');
        // 上传区域置空
        this.$store.dispatch('setUpload');
        // store传递类型以及文件信息
        this.$store.dispatch('setImgMsg', {
          type: 'image/jpeg',
          name,
          url: URL,
          width: canvasWidthHeight.canvasWidth,
          height: canvasWidthHeight.canvasHeight,
        });
      },
      getBase64Image(img) {
        const canvasWidthHeight = this.calCanvasWidthAndHeight(img.dataset.width, img.dataset.height);
        const canvas = document.createElement('canvas');
        canvas.width = canvasWidthHeight.canvasWidth;
        canvas.height = canvasWidthHeight.canvasHeight;
        const ctx = canvas.getContext('2d');
        ctx.drawImage(img, 0, 0, canvas.width, canvas.height);
        const dataURL = canvas.toDataURL('image/jpeg');
        return dataURL;
      },
    },
  };
</script>

<style scoped>
  .upload {
    width: 100%;
    height: 100%;
  }
  .import-area {
    position: absolute;
    left: 50%;
    top: 50%;
    margin: -120px -150px;
    box-sizing: border-box;
    width: 300px;
    height: 200px;
    background: #2b3342;
    border: 2px dashed #7d7d7d;
    border-radius: 10px;
    cursor: pointer;
    transition: 0.1s;
  }

  .import-area:hover {
    background: #324057;
    border-color: #ccc;
  }

  .import-area .icon {
    display: inline-block;
    margin-top: 20px;
    width: 128px;
    height: 128px;
    background: url('../../../../static/sprites/upload.png') no-repeat;
  }

  .import-area .tip {
    margin: 0;
    text-align: center;
    font-size: 14px;
    color: #999;
  }

  .import-area .tip .browse {
    color: #7D5CFF;
  }

  .import-area:hover .browse {
    text-decoration: underline;
  }

  .primary-pic-ct {
    height: 450px;
    overflow-x: hidden;
    overflow-y: auto;
  }

  .chrome-scrollbar::-webkit-scrollbar {
    width: 6px;
    margin-right: 5px;
  }

  .chrome-scrollbar::-webkit-scrollbar-track {
    background-color: #eee;
  }

  .chrome-scrollbar::-webkit-scrollbar-thumb {
    border-radius: 20px;
    background-color: rgba(0, 0, 0, .15);
  }

  .chrome-scrollbar:hover::-webkit-scrollbar-thumb {
    background-color: rgba(0, 0, 0, .25)
  }
  .el-dialog--small{
    width: 70%;
  }
  .el-row {
    &:last-child {
      margin-bottom: 0;
    }
  }
  .el-col {
    margin-bottom: 10px;
    overflow: auto;
    cursor: pointer;
  }
  .el-dialog__body {
    padding: 20px;
  }
  .select-img {
    width: 100%;
    vertical-align: top;
    transition: 0.5s;
  }
  .select-img:hover {
    -webkit-transform: scale(1.2);
    -ms-transform: scale(1.2);
    transform: scale(1.2);
  }
  .upload-btn {
    position: relative;
    margin-top: 10px;
    width: 100%;
    height: 100px;
    line-height: 108px;
    border-radius: 10px;
    box-sizing: border-box;
    border: 3px dashed #ccc;
    transition: 0.2s;
  }
  .upload-btn:hover {
    background: #eee;
  }
  .upload-icon {
    display: inline-block;
    width: 32px;
    height: 32px;
    background: url('../../../../static/sprites/plus.png') no-repeat;
  }
  .sr-only {
    cursor: pointer;
  }
  .el-dialog__header {
    text-align: left;
  }
  .v-modal {
    opacity: 0.6;
  }


  @media screen and (max-width: 1600px) {
    .primary-pic-ct {
      height: 300px;
    }

    .upload-btn{
      height: 80px;
      line-height: 88px;
    }
  }
</style>
