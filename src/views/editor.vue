<template>
  <div :class="[editorIsFullscrean ? 'fullscreen' : '', 'editor']">
    <div v-show="showToolbar" class="toolbar-box">
      <toolbar-left
        class="left"
        @addImg="addImg"
        @addFile="addFile"
        :dom="getTextarea"
        :customLeftToolbar="customLeftToolbar"
        :bold="bold"
        :italic="italic"
        :h1="h1"
        :h2="h2"
        :h3="h3"
        :h4="h4"
        :h5="h5"
        :underline="underline"
        :throughline="throughline"
        :mark="mark"
        :sub="sub"
        :sup="sup"
        :left="left"
        :center="center"
        :right="right"
        :quotation="quotation"
        :ul="ul"
        :ol="ol"
        :code="code"
        :table="table"
        :link="link"
        :linkImage="linkImage"
        :fileImage="fileImage"
        :linkVideo="linkVideo"
        :fileVideo="fileVideo"
        :linkAttachment="linkAttachment"
        :fileAttachment="fileAttachment"
        :clear="clear"
      >
        <slot name="toolbarLeftBefore" slot="toolbarLeftBefore"></slot>
        <slot name="toolbarLeftAfter" slot="toolbarLeftAfter"></slot>
      </toolbar-left>
      <toolbar-right
        class="right"
        :dom="getRenderHtml"
        :helpDoc="helpDoc"
        :customRightToolbar="customRightToolbar"
        :split="split"
        :fullscreen="fullscreen"
        :importBtn="importBtn"
        :exportBtn="exportBtn"
        :printBtn="printBtn"
        :helpBtn="helpBtn"
      >
        <slot name="toolbarRightBefore" slot="toolbarRightBefore"></slot>
        <slot name="toolbarRightAfter" slot="toolbarRightAfter"></slot>
      </toolbar-right>
    </div>
    <div class="markdown-body">
      <div
        v-show="showTextarea"
        :class="[editorIsSplit ? 'split' : 'one', 'markdown-input']"
        @scroll="editOnScroll"
        @click="textareaFocus"
        ref="inputEdit"
      >
        <auto-textarea ref="autoTextarea" v-model="localValue"></auto-textarea>
      </div>
      <div
        ref="renderHtml"
        class="markdown-render"
        :class="[showTextarea ? (editorIsSplit ? 'split' : 'hidden') : '']"
      >
        <div v-html="renderValue"></div>
      </div>
      <transition name="fade">
        <div
          @click="imgPreviewSrc = null"
          class="img-preview"
          v-if="imgPreviewSrc"
        >
          <div class="img-box">
            <img
              :src="imgPreviewSrc"
              :alt="$t('toolbar.editor.preview')"
              :style="{
                height: `${imgHeight}`,
              }"
            />
            <div class="img-op" @click="opClick">
              <i class="iconfont icon-zoom-in" @click="zoomIn"></i>
              <i class="iconfont icon-zoom-out" @click="zoomOut"></i>
            </div>
          </div>
        </div>
      </transition>
    </div>

    <div ref="copyHtml" class="copy-html">
      <div v-html="renderValue"></div>
    </div>
  </div>
</template>
<script>
import "../assets/fonts/iconfont.css";
import "../assets/less/reset.less";
import "../assets/less/tooltip.less";
import "../assets/less/github-markdown.css";
import ToolbarLeft from "../components/toolbar/toolbar-left";
import ToolbarRight from "../components/toolbar/toolbar-right";
import AutoTextarea from "../components/auto-textarea";
import { mapGetters, mapActions } from "vuex";
import { keyboardListener } from "../utils/keyboard-listener";
import { insertContentAtCaret } from "../utils/insert";
import loader from "../utils/loader";
import external from "../config/external";
import md from "../utils/md";
import { scrollLink } from "../utils/scroll";
// 注册指令和组件
import { VTooltip, VPopover, VClosePopover } from "v-tooltip";
import Vue from "vue";
import { i18n } from "../setup/i18n-setup";
Vue.directive("tooltip", VTooltip);
Vue.directive("close-popover", VClosePopover);
Vue.component("v-popover", VPopover);
export default {
  name: "editor",
  data() {
    return {
      imgPreviewSrc: null,
      imgHeight: "70%",
      zoomStep: 10,
    };
  },
  props: {
    uploadFileFn: {
      type: Function,
      default: () =>
        new Promise((resolve, reject) => {
          setTimeout(() => {
            resolve({ upload: false, url: "http://www.baidu.com" }); // uploadFileFn required the return value {upload: true, url: 'the real url'}
          }, 0);
        }),
    },
    uploadImgFn: {
      type: Function,
      default: () =>
        new Promise((resolve, reject) => {
          setTimeout(() => {
            resolve({ upload: false, url: "http://www.baidu.com" }); // uploadImgFn required the return value {upload: true, url: 'the real url'}
          }, 0);
        }),
    },
    showToolbar: {
      type: Boolean,
      default: true,
    },
    showTextarea: {
      type: Boolean,
      default: true,
    },
    value: {
      type: String,
      default: "",
    },
    plugins: {
      type: Object,
      default: () => ({}),
    },
    helpDoc: {
      type: String,
      default: "",
    },
    customLeftToolbar: {
      type: Boolean,
      default: false,
    },
    customRightToolbar: {
      type: Boolean,
      default: false,
    },
    imgWidthHeightAttr: {
      type: Object,
      default: () => ({
        width: false,
        height: false,
      }),
    },
    imageClickHandler: {
      type: Function,
      default: null,
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
    split: {
      type: Boolean,
      default: true,
    },
    fullscreen: {
      type: Boolean,
      default: true,
    },
    importBtn: {
      type: Boolean,
      default: true,
    },
    exportBtn: {
      type: Boolean,
      default: true,
    },
    printBtn: {
      type: Boolean,
      default: true,
    },
    helpBtn: {
      type: Boolean,
      default: true,
    }
  },
  components: {
    ToolbarLeft,
    ToolbarRight,
    AutoTextarea,
  },
  i18n,
  mounted() {
    this.showToolbar && keyboardListener(this.getTextarea(), this);
    this.imagePreviewListener();
    this.localValue = this.value;
  },
  async created() {
    md.init(this.plugins);
    // option inject
    if (this.plugins.katex) {
      loader.loadLink(external.css.katex); // config
    }
    // option inject
    if (this.plugins.mathjax) {
      loader.loadScript(external.js.mathjax, (obj) => {
        // todos: split MathJax init
        window.MathJax &&
          window.MathJax.Hub.Config({
            extensions: ["tex2jax.js"],
            jax: ["input/TeX", "output/HTML-CSS"],
            tex2jax: {
              inlineMath: [
                ["$", "$"],
                ["\\(", "\\)"],
              ],
              displayMath: [
                ["$$", "$$"],
                ["\\[", "\\]"],
              ],
              processEscapes: true,
            },
            "HTML-CSS": { fonts: ["TeX"] },
          });
      });
    }
  },
  computed: {
    ...mapGetters({
      editorIsSplit: "markdownBody/getEditorIsSplit",
      editorIsFullscrean: "markdownBody/getEditorIsFullscrean",
      textareaContent: "markdownBody/getTextareaContent",
    }),
    renderValue() {
      return this.render(this.textareaContent);
    },
    localValue: {
      set(val) {
        this.setTextareaContent(val);
        this.$emit("input", val);
      },
      get() {
        return this.textareaContent;
      },
    },
  },
  methods: {
    ...mapActions({ setTextareaContent: "markdownBody/setTextareaContent" }),
    getTextarea() {
      if (this.$refs["autoTextarea"]) {
        return this.$refs["autoTextarea"].$refs["textarea"];
      }
    },
    getRenderHtml() {
      if (this.$refs["renderHtml"]) {
        return this.$refs["renderHtml"];
      }
    },
    async addImg(index, file, multiple) {
      const ret = await this.uploadImgFn(file);
      // width height
      let wh = {};
      let payload = { name: file.name };
      if (this.imgWidthHeightAttr.width || this.imgWidthHeightAttr.height) {
        wh = await this.getWH(file);
        this.imgWidthHeightAttr.width && (payload.width = wh.width);
        this.imgWidthHeightAttr.height && (payload.height = wh.height);
      }
      if (ret.upload) {
        payload.url = ret.url;
        payload.multiple = multiple;
        insertContentAtCaret(this.getTextarea, "image", payload, this);
      } else {
        const reader = new FileReader();
        reader.onload = (e) => {
          md.imageAdd(index, e.target.result); // plugin
          payload.url = index;
          insertContentAtCaret(this.getTextarea, "image", payload, this);
        };

        file && reader.readAsDataURL(file);
      }
    },
    async addVideo(file, multiple) {
      const ret = await this.uploadImgFn(file);
      // width height
      let payload = { name: file.name };
      if (ret.upload) {
        payload.url = ret.url;
        payload.multiple = multiple;
        insertContentAtCaret(this.getTextarea, "video", payload, this);
      } else {
        const reader = new FileReader();
        reader.onload = (e) => {
          payload.url = e.target.result; // 本地base64
          insertContentAtCaret(this.getTextarea, "video", payload, this);
        };

        file && reader.readAsDataURL(file);
      }
    },
    async getWH(file) {
      return new Promise((resolve, reject) => {
        const image = new Image();
        image.onload = () => {
          const width = image.width;
          const height = image.height;
          resolve({ width, height });
        };
        image.onerror = reject;
        image.src = URL.createObjectURL(file);
      });
    },
    async addFile(file) {
      const ret = await this.uploadFileFn(file);
      if (ret.upload) {
        insertContentAtCaret(
          this.getTextarea,
          "link",
          { name: "【附件】" + file.name, url: ret.url },
          this
        );
      } else {
        alert("please config the props uploadFileFn");
      }
    },
    render(val) {
      // inject
      let result = val;
      if (this.plugins.mathjax) {
        setTimeout(() => {
          // todos: split MathJax render
          const copyDom = document.getElementsByClassName("copy-html");
          const renderDom = document.getElementsByClassName("markdown-render");
          window.MathJax &&
            window.MathJax.Hub.Queue([
              "Typeset",
              window.MathJax.Hub,
              copyDom,
              () => {
                const copy = copyDom[0] && copyDom[0].innerHTML;
                renderDom[0] && (renderDom[0].innerHTML = copy);
              },
            ]);
        }, 200);
        // todos: split transform the code latex => mathajx
        result = val.replace(/\${1,2}[\s\S]*(\\\\)[\s\S]*\${1,2}/g, (match) => {
          return match.replace(/\\\\/g, `\\cr`);
        });

        /* .replace(/\${1,2}[\s\S]*_[\s\S]*\${1,2}/g, (match) => { // em conflict
                    return match.replace(/_/g, `\\_`) // bow to markdown
                }) */
      }
      return md.render(result);
    },
    imagePreviewListener() {
      // renderHtml img click
      this.$refs.renderHtml.addEventListener("click", (e) => {
        this.imgHeight = "70%";
        const event = e || window.event;
        const ele = event.srcElement || event.target;
        if (ele.tagName === "IMG") {
          if (this.imageClickHandler) {
            this.imageClickHandler(ele);
          } else {
            this.imgPreviewSrc = ele.src;
          }
        }
      });
    },
    opClick(e) {
      e.stopPropagation();
      e.preventDefault();
      return false;
    },
    zoomIn() {
      let width = +this.imgHeight.split("%")[0];
      let result = width - this.zoomStep > 0 ? width - this.zoomStep : 0;
      this.imgHeight = `${result}%`;
    },
    zoomOut() {
      let width = +this.imgHeight.split("%")[0];
      let result = width + this.zoomStep > 0 ? width + this.zoomStep : 0;
      this.imgHeight = `${result}%`;
    },
    editOnScroll(e) {
      scrollLink(e, this);
    },
    textareaFocus() {
      this.$refs["autoTextarea"].$refs["textarea"].focus();
    },
  },
  watch: {
    value: function (val) {
      this.setTextareaContent(val);
    },
  },
};
</script>
<style lang="less" scoped>
.editor {
  display: flex;
  flex-direction: column;
  width: 100%;
  border: none;
  box-shadow: 0 0px 3px#ccc;
  background: #fff;
  min-height: 400px;
  margin: 20px auto;
  &.fullscreen {
    position: fixed;
    left: 0;
    right: 0;
    bottom: 0;
    top: 0;
    height: auto;
    z-index: 1501;
    margin: 0;
  }
  .toolbar-box {
    display: flex;
    flex-wrap: wrap;
    padding: 10px;
    border-bottom: 1px solid #ccc;
    .left {
      flex: 3;
      justify-content: flex-start;
    }
    .right {
      flex: 1;
      justify-content: flex-end;
    }
  }
  .markdown-body {
    display: flex;
    // min-height: 400px;
    flex: 1;
    overflow: hidden;
    .img-preview {
      display: flex;
      align-items: center;
      justify-content: center;
      position: fixed;
      left: 0;
      right: 0;
      top: 0;
      bottom: 0;
      background: rgba(0, 0, 0, 0.7);
      z-index: 1600;
      transition: all 0.1s linear 0s;
      .img-box {
        width: 100%;
        height: 100%;
        overflow: auto;
        img {
          max-width: initial;
          position: absolute;
          margin: auto;
          top: 0;
          bottom: 0;
          left: 0;
          right: 0;
        }
        .img-op {
          position: absolute;
          left: 50%;
          bottom: 20px;
          background-color: #333333;
          border-radius: 5px;
          padding: 0 12px;
          i {
            display: inline-block;
            font-size: 20px;
            padding: 10px;
            color: #fff;
            &:hover {
              background: #e9e8e8;
              color: #333;
            }
          }
        }
      }
    }

    .markdown-input {
      padding: 10px 20px;
      overflow-y: auto;
      overflow-x: hidden;
    }
    .split {
      width: 50%;
      box-shadow: 0 0px 3px#ccc;
    }
    .one {
      width: 100%;
    }
    .hidden {
      display: none;
    }
    .markdown-render {
      text-align: left;
      padding: 10px 26px;
      overflow-y: auto;
      overflow-x: hidden;
    }
  }
  .copy-html {
    display: none;
  }
}
</style>
