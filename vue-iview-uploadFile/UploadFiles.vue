<template>
  <div>
    <Upload
      ref="fileUpload"
      :default-file-list="defaultList"
      :action="fileUrl"
      :max-size="fileSize"
      :before-upload="handleBeforeUpload"
      :on-success="handleSuccess"
      :on-exceeded-size="handleMaxSize"
      :on-remove="handleRemove"
      :accept="accept"
    >
     <!-- :format="format" -->
      <a href="javascript:void(0);">点击上传</a>
    </Upload>
  </div>
</template>

<script>

export default {
  name: 'UploadFiles',
  data() {
    return {
      uploadList: 0,
      fileResArr: [],
      accept: '.doc,.docx,.xls,.xlsx,.xla,.csv,.xlsm,.txt,.xps,.xlam,.xml,.pdf,.jpg,.png,.gif,.jpge',
      format: [
        '.doc',
        '.docx',
        '.xls',
        '.xlsx',
        '.xla',
        '.csv',
        '.xlsm',
        '.txt',
        '.xps',
        '.xlam',
        '.xml',
        '.pdf',
        '.jpg',
        '.png',
        '.gif',
        '.jpge'
      ]
    };
  },
  props: {
    // 默认文件列表（给默认值或编辑时可用）
    defaultList: {
      type: Array,
      default() {
        return [];
      }
    },
    // 文件限制数量
    fileNum: {
      type: Number,
      default() {
        return 5;
      }
    },
    // 文件限制大小
    fileSize: {
      type: Number,
      default() {
        return 10240;
      }
    },
    // 文件链接
    fileUrl: {
      type: String,
      default() {
        return '';
      }
    }
  },
  methods: {
    handleBeforeUpload() {
      const check = this.uploadList <= this.fileNum-1;
      if (!check) {
        this.$Notice.warning({
          title: '最多只能上传5个文件'
        });
      } else {
        this.uploadList += 1;
      }
      return check;
    },
    handleSuccess(res) {
      this.fileResArr.push(res.data);
      this.$emit('getFileData', this.fileResArr);
    },
    handleMaxSize() {
      this.$Notice.warning({
        title: '文件大小超出限制，请上传10M以下的文件'
      });
    },
    handleRemove(file) {
      let removeFile = '';
      if (file.response) {
        removeFile = file.response.data;
      } else {
        removeFile = file.data;
      }
      this.fileResArr.splice(this.fileResArr.indexOf(removeFile), 1);
      this.uploadList -= 1;
      this.$emit('getFileData', this.fileResArr);
    }
  },
  mounted() {
    // 初始化组件内容
    if (this.$refs.upload && this.$refs.upload.fileList) {
      this.$nextTick(() => {
        this.fileResArr=[]
        // this.uploadList = this.$refs.upload.fileList
      });
    }
  },
  watch: {
    defaultList(val) {
      this.fileResArr=[]
      val.map(el => {
        this.fileResArr.push(el.data);
      });
    }
  }
};
</script>

<style scoped></style>
