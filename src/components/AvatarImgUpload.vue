<template>
  <div>
    <div
      class="image-container"
      @click="openFileUpload();"
      @drop.prevent.stop="onDrop($event);"
      @dragenter.prevent.stop="onDragEnterAndOver($event);"
      @dragover.prevent.stop="onDragEnterAndOver($event);"
    >
      <img :src="imgSrc" alt="User Avatar here" id="avatar" />
      <input
        type="file"
        name="fileInput"
        id="fileInput"
        ref="avatarRef"
        accept="image/*"
        @change="onFileUpload();"
      />
      <i class="fas fa-trash-alt remove-image" @click.stop="removeImage();"></i>
      <div
        class="loading-overlay"
        @click.stop="preventClick();"
        v-if="isUploading"
      >
        <i class="fas fa-spinner fa-spin"></i>
      </div>
    </div>
    <p class="error-message">{{ errorMessage }}</p>
  </div>
</template>

<script>
import pretty from "prettysize";
import defaultImage from "../assets/logo.png";

export default {
  name: "AvatarImgUpload",
  props: {
    src: {
      type: String
    },
    maxSize: {
      type: Number,
      default: 2097152
    },
    isUploading: {
      type: Boolean,
      default: false
    }
  },
  data() {
    return {
      file: {},
      uploadedImageSrc: "",
      errorMessage: ""
    };
  },
  methods: {
    preventClick() {
      // placeholder to prevent click on the overlay
    },
    onDragEnterAndOver(event) {
      event.dataTransfer.dropEffect = "move";
    },
    testFile(file) {
      this.errorMessage = "";
      const humanReadableSize = pretty(this.maxSize);
      if (!file) {
        return true;
      }

      if (!/image(\/{1})(jpeg|png|jpg|gif)/.test(file.type)) {
        this.errorMessage = `Wrong filetype, please upload a png, jpeg, jpg or a gif file`;
        return true;
      }

      if (file.size > this.maxSize) {
        this.errorMessage = `File size exceeds ${humanReadableSize}, please upload an image less than ${humanReadableSize}`;
        return true;
      }

      return;
    },
    onDrop(event) {
      const uploadedFile = event.dataTransfer.files[0];

      if (this.testFile(uploadedFile)) {
        return;
      }

      this.file = uploadedFile;
      this.renderImage(this.file);
    },
    openFileUpload() {
      this.$refs.avatarRef.click();
    },
    onFileUpload() {
      const uploadedFile = this.$refs.avatarRef.files[0];

      if (this.testFile(uploadedFile)) {
        return;
      }

      this.file = uploadedFile;
      this.renderImage(this.file);
    },
    renderImage(file) {
      const fileReader = new FileReader();
      fileReader.onload = event => {
        this.uploadedImageSrc = event.target.result;
        this.$emit("image-upload", this.file);
      };
      fileReader.readAsDataURL(file);
    },
    removeImage() {
      this.file = "";
      this.uploadedImageSrc = "";
      this.$emit("remove-image");
    }
  },
  computed: {
    imgSrc() {
      if (!this.isUploading && this.src) {
        return this.src;
      }

      return this.uploadedImageSrc || defaultImage;
    }
  },
  watch: {
    isUploading(newValue, oldValue) {
      if (newValue !== oldValue && newValue === false) {
        this.uploadedImageSrc = "";
      }
    }
  }
};
</script>

<style scoped>
.image-container {
  height: 100px;
  width: 100px;
  border: 5px solid #666;
  border-radius: 50%;
  overflow: hidden;
  position: relative;
  cursor: pointer;
}

#fileInput {
  visibility: hidden;
}

.error-message {
  color: red;
}

#avatar {
  height: 100px;
  width: 100px;
  object-fit: cover;
}

.remove-image {
  color: #666;
  position: absolute;
  bottom: 15px;
  right: 15px;
  font-size: 15px;
  opacity: 0.4;
  z-index: 999;
}

.remove-image:hover {
  opacity: 0.7;
}

.loading-overlay {
  height: 100%;
  width: 100%;
  position: absolute;
  right: 0;
  bottom: 0;
  display: flex;
  align-items: center;
  justify-content: center;
  background-color: #999;
  opacity: 0.4;
}

.loading-overlay i {
  font-size: 35px;
}
</style>
