<template>
  <div class="icon-box">
    <template v-if="customLeftToolbar"> </template>
    <template v-else>
      <slot name="toolbarLeftBefore"></slot>
      <span
        v-if="bold"
        v-tooltip.top-center="$t('toolbar.left.bold')"
        @click="clickHandler('bold', 'insert')"
        ><i class="iconfont icon-bold"></i
      ></span>
      <span
        v-if="italic"
        v-tooltip.top-center="$t('toolbar.left.italic')"
        @click="clickHandler('italic', 'insert')"
        ><i class="iconfont icon-italic"></i
      ></span>
      <span
        v-if="h1 || h2 || h3 || h4 || h5"
        class="menu"
        v-tooltip.top-center="$t('toolbar.left.title')"
        @click="clickHandler('title', 'menu')"
        @mouseenter="showTitleMenu"
        @mouseleave="hideTitleMenu"
      >
        <i class="iconfont icon-title"></i>
        <transition name="fade">
          <ul
            v-show="titleMenuShow"
            :class="['icon-menu']"
            @mouseenter="showTitleMenu"
            @mouseleave="hideTitleMenu"
          >
            <li v-if="h1" @click="clickHandler('title1', 'insert')">
              {{ $t("toolbar.left.h1") }}
            </li>
            <li v-if="h2" @click="clickHandler('title2', 'insert')">
              {{ $t("toolbar.left.h2") }}
            </li>
            <li v-if="h3" @click="clickHandler('title3', 'insert')">
              {{ $t("toolbar.left.h3") }}
            </li>
            <li v-if="h4" @click="clickHandler('title4', 'insert')">
              {{ $t("toolbar.left.h4") }}
            </li>
            <li v-if="h5" @click="clickHandler('title5', 'insert')">
              {{ $t("toolbar.left.h5") }}
            </li>
          </ul>
        </transition>
      </span>
      <span
        v-if="underline"
        v-tooltip.top-center="$t('toolbar.left.underline')"
        @click="clickHandler('underline', 'insert')"
        ><i class="iconfont icon-underline"></i
      ></span>
      <span
        v-if="throughline"
        v-tooltip.top-center="$t('toolbar.left.throughline')"
        @click="clickHandler('throughline', 'insert')"
        ><i class="iconfont icon-strike"></i
      ></span>
      <span
        v-if="mark"
        v-tooltip.top-center="$t('toolbar.left.mark')"
        @click="clickHandler('mark', 'insert')"
        ><i class="iconfont icon-mark"></i
      ></span>
      <span
        v-if="sub"
        v-tooltip.top-center="$t('toolbar.left.sub')"
        @click="clickHandler('sub', 'insert')"
        ><i class="iconfont icon-sub"></i
      ></span>
      <span
        v-if="sup"
        v-tooltip.top-center="$t('toolbar.left.sup')"
        @click="clickHandler('sup', 'insert')"
        ><i class="iconfont icon-sup"></i
      ></span>
      <span
        v-if="left"
        v-tooltip.top-center="$t('toolbar.left.left')"
        @click="clickHandler('left', 'insert')"
        ><i class="iconfont icon-left"></i
      ></span>
      <span
        v-if="center"
        v-tooltip.top-center="$t('toolbar.left.center')"
        @click="clickHandler('center', 'insert')"
        ><i class="iconfont icon-center"></i
      ></span>
      <span
        v-if="right"
        v-tooltip.top-center="$t('toolbar.left.right')"
        @click="clickHandler('right', 'insert')"
        ><i class="iconfont icon-right"></i
      ></span>
      <span
        v-if="quotation"
        v-tooltip.top-center="$t('toolbar.left.quotation')"
        @click="clickHandler('quotation', 'insert')"
        ><i class="iconfont icon-quotation"></i
      ></span>
      <span
        v-if="ul"
        v-tooltip.top-center="$t('toolbar.left.ul')"
        @click="clickHandler('ul', 'insert')"
        ><i class="iconfont icon-ul"></i
      ></span>
      <span
        v-if="ol"
        v-tooltip.top-center="$t('toolbar.left.ol')"
        @click="clickHandler('ol', 'insert')"
        ><i class="iconfont icon-ol"></i
      ></span>
      <span
        v-if="code"
        v-tooltip.top-center="$t('toolbar.left.code')"
        @click="clickHandler('code', 'insert')"
        ><i class="iconfont icon-code"></i
      ></span>
      <span
        v-if="table"
        v-tooltip.top-center="$t('toolbar.left.table')"
        @click="clickHandler('table', 'insert')"
        ><i class="iconfont icon-table"></i
      ></span>
      <span
        v-if="link"
        v-tooltip.top-center="$t('toolbar.left.link')"
        @click="clickHandler('link', 'pop')"
        ><i class="iconfont icon-link"></i
      ></span>
      <span
        v-if="linkImage || fileImage"
        v-tooltip.top-center="$t('toolbar.left.image')"
        class="menu"
        @click="clickHandler('image', 'menu')"
        @mouseenter="showImageMenu"
        @mouseleave="hideImageMenu"
      >
        <i class="iconfont icon-image"></i>
        <transition name="fade">
          <ul
            v-show="imageMenuShow"
            :class="['icon-menu']"
            @mouseenter="showImageMenu"
            @mouseleave="hideImageMenu"
          >
            <li v-if="linkImage" @click="addImgFromLink">
              {{ $t("toolbar.left.fromNetwork") }}
            </li>
            <li v-if="fileImage">
              <input
                type="file"
                accept="image/gif,image/jpeg,image/jpg,image/png,image/svg"
                @change="(e) => addImgFromLocal(e)"
                multiple="multiple"
              />
              {{ $t("toolbar.left.fromLocal") }}
            </li>
          </ul>
        </transition>
      </span>
      <span
        v-if="linkVideo || fileVideo"
        v-tooltip.top-center="$t('toolbar.left.video')"
        class="menu"
        @click="clickHandler('video', 'menu')"
        @mouseenter="showVideoMenu"
        @mouseleave="hideVideoMenu"
      >
        <i class="iconfont icon-video"></i>
        <transition name="fade">
          <ul
            v-show="videoMenuShow"
            :class="['icon-menu']"
            @mouseenter="showVideoMenu"
            @mouseleave="hideVideoMenu"
          >
            <li v-if="linkVideo" @click="addVideFromLink">
              {{ $t("toolbar.left.fromNetwork") }}
            </li>
            <li v-if="fileVideo">
              <input
                type="file"
                accept="video/mp4,audio/mp4"
                @change="(e) => addVideoFromLocal(e)"
              />
              {{ $t("toolbar.left.fromLocal") }}
            </li>
          </ul>
        </transition>
      </span>
      <span
        v-if="linkAttachment || fileAttachment"
        v-tooltip.top-center="$t('toolbar.left.attachment')"
        @click="clickHandler('file', 'menu')"
        @mouseenter="showFileMenu"
        @mouseleave="hideFileMenu"
      >
        <i class="iconfont icon-attachment"></i>
        <transition name="fade">
          <ul
            v-show="fileMenuShow"
            :class="['icon-menu']"
            @mouseenter="showFileMenu"
            @mouseleave="hideFileMenu"
          >
            <li v-if="linkAttachment" @click="addFileFromLink">
              {{ $t("toolbar.left.fromNetwork") }}
            </li>
            <li v-if="fileAttachment">
              <input
                type="file"
                accept="*"
                @change="(e) => addFileFromLocal(e)"
              />{{ $t("toolbar.left.fromLocal") }}
            </li>
          </ul>
        </transition>
      </span>
      <span
        v-if="clear"
        v-tooltip.top-center="$t('toolbar.left.clear')"
        @click="clickHandler('clear', 'clear')"
      >
        <i class="iconfont icon-trash"></i>
      </span>
      <slot name="toolbarLeftAfter"></slot>
      <div v-show="imagePopShow" class="image-pop">
        <div class="dialog">
          <div class="input-box">
            <input
              :placeholder="$t('toolbar.left.imageName')"
              v-model="imageName"
              type="text"
            />
          </div>
          <div class="input-box">
            <input
              :placeholder="$t('toolbar.left.imageLink')"
              v-model="imageUrl"
              type="text"
            />
          </div>
          <div class="btn-box">
            <div @click="closePop('imagePopShow')">
              {{ $t("toolbar.left.cancel") }}
            </div>
            <div class="confirm" @click="clickHandler('image', 'insert')">
              {{ $t("toolbar.left.confirm") }}
            </div>
          </div>
        </div>
      </div>
      <div v-show="videoPopShow" class="video-pop">
        <div class="dialog">
          <div class="input-box">
            <input
              :placeholder="$t('toolbar.left.videoLink')"
              v-model="videoUrl"
              type="text"
            />
          </div>
          <div class="btn-box">
            <div @click="closePop('videoPopShow')">
              {{ $t("toolbar.left.cancel") }}
            </div>
            <div class="confirm" @click="clickHandler('video', 'insert')">
              {{ $t("toolbar.left.confirm") }}
            </div>
          </div>
        </div>
      </div>
      <div v-show="linkPopShow" class="link-pop">
        <div class="dialog">
          <div class="input-box">
            <input
              :placeholder="$t('toolbar.left.linkName')"
              v-model="linkName"
              type="text"
            />
          </div>
          <div class="input-box">
            <input
              :placeholder="$t('toolbar.left.link')"
              v-model="linkUrl"
              type="text"
            />
          </div>
          <div class="btn-box">
            <div @click="closePop('linkPopShow')">
              {{ $t("toolbar.left.cancel") }}
            </div>
            <div class="confirm" @click="clickHandler('link', 'insert')">
              {{ $t("toolbar.left.confirm") }}
            </div>
          </div>
        </div>
      </div>
      <div v-show="filePopShow" class="file-pop">
        <div class="dialog">
          <div class="input-box">
            <input
              v-model="fileName"
              :placeholder="$t('toolbar.left.attachmentName')"
              type="text"
            />
          </div>
          <div class="input-box file-box">
            <input
              :placeholder="$t('toolbar.left.attachmentLink')"
              v-model="fileUrl"
              type="text"
            />
          </div>
          <div class="btn-box">
            <div @click="closePop('filePopShow')">
              {{ $t("toolbar.left.cancel") }}
            </div>
            <div class="confirm" @click="clickHandler('file', 'insert')">
              {{ $t("toolbar.left.confirm") }}
            </div>
          </div>
        </div>
      </div>
    </template>
  </div>
</template>
<script>
import { insertContentAtCaret } from "../../utils/insert";
import { mapActions } from "vuex";

export default {
  data() {
    return {
      imageMenuShow: false,
      videoMenuShow: false,
      fileMenuShow: false,
      titleMenuShow: false,
      menuShowTimer: null,
      videoShowTimer: null,
      linkPopShow: false,
      imagePopShow: false,
      videoPopShow: false,
      filePopShow: false,
      fileName: this.$t("toolbar.left.fileName"),
      fileUrl: "",
      imageName: "",
      imageUrl: "",
      videoUrl: "",
      linkName: "",
      linkUrl: "",
      imgIndex: 0,
    };
  },
  props: {
    dom: {
      default: () => {},
    },
    bold: {
      type: Boolean,
      default: true,
    },
    italic: {
      type: Boolean,
      default: true,
    },
    h1: {
      type: Boolean,
      default: true,
    },
    h2: {
      type: Boolean,
      default: true,
    },
    h3: {
      type: Boolean,
      default: true,
    },
    h4: {
      type: Boolean,
      default: true,
    },
    h5: {
      type: Boolean,
      default: true,
    },
    underline: {
      type: Boolean,
      default: true,
    },
    throughline: {
      type: Boolean,
      default: true,
    },
    mark: {
      type: Boolean,
      default: true,
    },
    sub: {
      type: Boolean,
      default: true,
    },
    sup: {
      type: Boolean,
      default: true,
    },
    left: {
      type: Boolean,
      default: true,
    },
    center: {
      type: Boolean,
      default: true,
    },
    right: {
      type: Boolean,
      default: true,
    },
    quotation: {
      type: Boolean,
      default: true,
    },
    ul: {
      type: Boolean,
      default: true,
    },
    ol: {
      type: Boolean,
      default: true,
    },
    code: {
      type: Boolean,
      default: true,
    },
    table: {
      type: Boolean,
      default: true,
    },
    link: {
      type: Boolean,
      default: true,
    },
    linkImage: {
      type: Boolean,
      default: true,
    },
    fileImage: {
      type: Boolean,
      default: true,
    },
    linkVideo: {
      type: Boolean,
      default: true,
    },
    fileVideo: {
      type: Boolean,
      default: true,
    },
    linkAttachment: {
      type: Boolean,
      default: true,
    },
    fileAttachment: {
      type: Boolean,
      default: true,
    },
    clear: {
      type: Boolean,
      default: true,
    },
    customLeftToolbar: {
      type: Boolean,
      default: false,
    },
  },
  methods: {
    clickHandler(icon, type) {
      if (type === "menu") {
        this.menuHandler(icon);
      } else if (type === "pop") {
        this.popHandler(icon);
      } else if (type === "upload") {
        this.uploadHanler(icon);
      } else if (type === "insert") {
        if (icon.includes("title")) {
          this.titleMenuShow = false;
        }
        this.insertHandler(icon);
      } else if (type === "clear") {
        this.clearContent(icon);
      } else {
      }
    },
    menuHandler(icon) {},
    popHandler(icon) {
      if (icon === "link") {
        this.linkPopShow = true;
      }
    },
    insertHandler(icon) {
      let payload = {};
      if (icon === "image") {
        payload = { name: this.imageName, url: this.imageUrl };
      } else if (icon === "file") {
        payload = { name: this.fileName, url: this.fileUrl };
      } else if (icon === "link") {
        payload = { name: this.linkName, url: this.linkUrl };
      } else if (icon === "video") {
        payload = { url: this.videoUrl };
      } else {
        // default
      }
      insertContentAtCaret(this.dom, icon, payload, this);
      this[icon + "PopShow"] = false;
    },
    uploadHanler(icon) {
      this.filePopShow = true;
    },
    showImageMenu() {
      clearTimeout(this.menuShowTimer);
      this.imageMenuShow = true;
    },
    hideImageMenu() {
      this.menuShowTimer = setTimeout(() => {
        this.imageMenuShow = false;
      }, 100);
    },
    showVideoMenu() {
      clearTimeout(this.videoShowTimer);
      this.videoMenuShow = true;
    },
    hideVideoMenu() {
      this.videoShowTimer = setTimeout(() => {
        this.videoMenuShow = false;
      }, 100);
    },
    showFileMenu() {
      clearTimeout(this.fileMenuShowTimer);
      this.fileMenuShow = true;
    },
    hideFileMenu() {
      this.fileMenuShowTimer = setTimeout(() => {
        this.fileMenuShow = false;
      }, 100);
    },
    showTitleMenu() {
      clearTimeout(this.titleMenuShowTimer);
      this.titleMenuShow = true;
    },
    hideTitleMenu() {
      this.titleMenuShowTimer = setTimeout(() => {
        this.titleMenuShow = false;
      }, 100);
    },
    addImgFromLink() {
      this.imagePopShow = true;
    },
    addVideFromLink() {
      this.videoPopShow = true;
    },
    addFileFromLink() {
      this.filePopShow = true;
    },
    addImgFromLocal(e) {
      const files = e.target.files;
      const multiple = files.length > 1;
      if (files.length > 0) {
        [].forEach.call(files, (item, index) => {
          this.imgAddHandler(item, multiple);
        });
      }

      e.target.value = ""; // input初始化
    },
    addVideoFromLocal(e) {
      const files = e.target.files;
      console.log(files);
      if (files.length > 0) {
        [].forEach.call(files, (item, index) => {
          this.videoAddHandler(item);
        });
      }

      e.target.value = ""; // input初始化
    },
    imgAddHandler(file, multiple) {
      this.imgIndex++;
      this.$emit("addImg", this.imgIndex, file, multiple);
    },
    videoAddHandler(file, multiple) {
      this.$emit("addVideo", file, multiple);
    },
    addFileFromLocal(e) {
      // 文件上传目前支持单个
      const file = e.target.files[0];
      file && this.$emit("addFile", file);
    },
    closePop(pop) {
      this[pop] = false;
    },
    clearContent(icon) {
      this.setTextareaContent(""); // clear content
      this.dom().focus(); // focus
    },
    ...mapActions({ setTextareaContent: "markdownBody/setTextareaContent" }),
  },
};
</script>
<style lang="less" scoped>
.icon-box {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  i {
    font-size: 18px;
    cursor: pointer;
  }
  span {
    position: relative;
    padding: 6px;
    &:hover {
      background: #e9e8e8;
    }
  }
  .icon-menu {
    display: block;
    position: absolute;
    top: 60px;
    left: -40px;
    z-index: 99;
    width: 120px;
    box-shadow: 0 0px 3px #ccc;
    line-height: 1.5;
    background: #fff;
    padding: 20px 0;
    &.fade-enter-active,
    &.fade-leave-active {
      opacity: 1;
    }

    &.fade-enter,
    &.fade-leave-active {
      opacity: 0;
    }
    li {
      position: relative;
      text-align: center;
      &:hover {
        background: #e9e8e8;
      }
      input {
        position: absolute;
        top: 0;
        right: 0;
        width: 100%;
        height: 100%;
        opacity: 0;
      }
    }
  }
  .image-pop,
  .video-pop,
  .link-pop,
  .file-pop {
    position: fixed;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    background: rgba(0, 0, 0, 0.7);
    z-index: 1600;
    -webkit-transition: all 0.1s linear 0s;
    transition: all 0.1s linear 0s;
    .dialog {
      position: fixed;
      box-sizing: border-box;
      text-align: center;
      width: 34%;
      left: 34%;
      height: auto;
      padding: 40px;
      top: 25%;
      -webkit-transition: all 0.1s linear 0s;
      transition: all 0.1s linear 0s;
      z-index: 3;
      background: #fff;
      border-radius: 2px;
      box-shadow: 0 0px 5px rgba(255, 255, 255, 0.157),
        0 0px 5px rgba(255, 255, 255, 0.227);
      color: #333;
      font-size: 14px;
      input {
        height: 36px;
        line-height: 36px;
        width: 100%;
      }
      .input-box {
        padding: 6px;
      }
      .btn-box {
        display: flex;
        flex-wrap: wrap;
        align-items: center;
        justify-content: center;
        > div {
          flex: 1;
          border: 1px solid #ccc;
          height: 36px;
          line-height: 36px;
          margin: 6px;
          &.confirm {
            background: #409eff;
            color: #fff;
          }
        }
      }
    }
  }
}
</style>
