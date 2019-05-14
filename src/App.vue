<template>
  <div id="app">
    <AvatarImgUpload
      @image-upload="uploadFile"
      :src="src"
      :isUploading="uploading"
      @remove-image="removeImage"
    />
  </div>
</template>

<script>
import Axios from "axios";
import AvatarImgUpload from "./components/AvatarImgUpload";

const cloudName = "dqyhsobqa";
const unsignedUploadPreset = "ewvntos9";
const cloudinaryLink = `https://api.cloudinary.com/v1_1/${cloudName}/upload`;

export default {
  name: "App",
  components: {
    AvatarImgUpload
  },
  data() {
    return {
      src: "",
      uploading: false
    };
  },
  methods: {
    uploadFile(fileData) {
      this.uploading = true;
      const formData = new FormData();
      formData.append("file", fileData);
      formData.append("upload_preset", unsignedUploadPreset);
      Axios.post(cloudinaryLink, formData).then(res => {
        this.src = res.data.url;
        this.uploading = false;
      });
    },
    removeImage() {
      this.src = "";
    }
  }
};
</script>

<style>
#app {
}
</style>
