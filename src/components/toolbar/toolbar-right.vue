<template>
    <div class="icon-box">
        <template v-if="customRightToolbar"> </template>
        <template v-else>
            <slot name="toolbarRightBefore"></slot>
            <span
                v-if="split"
                @click="clickHandler('split')"
                v-tooltip.top-center="$t('toolbar.right.split')"
                :class="[iconActive.split ? 'selected' : '']"
            >
                <i class="iconfont icon-split"></i>
            </span>
            <span
                v-if="fullscreen"
                @click="clickHandler('fullscreen')"
                v-tooltip.top-center="$t('toolbar.right.fullscreen')"
            >
                <i
                    :class="[
                        iconActive.fullscreen ? 'icon-exit' : 'icon-expand',
                        'iconfont'
                    ]"
                ></i>
            </span>
            <span
                v-if="importBtn"
                class="import"
                v-tooltip.top-center="$t('toolbar.right.import')"
            >
                <i class="iconfont icon-import"></i>
                <input
                    type="file"
                    accept="text/markdown"
                    @change="e => addMDFromLocal(e)"
                />
            </span>
            <span
                v-if="exportBtn"
                class="export"
                @click="exportMd"
                v-tooltip.top-center="$t('toolbar.right.export')"
            >
                <i class="iconfont icon-export"></i>
            </span>
            <span
                v-if="printBtn"
                @click="doPrint"
                v-tooltip.top-center="$t('toolbar.right.print')"
                ><i class="iconfont icon-print"></i
            ></span>
            <span
                v-if="helpBtn"
                @click="doHelp"
                v-tooltip.top-center="$t('toolbar.right.help')"
                ><i class="iconfont icon-help"></i
            ></span>
            <slot name="toolbarRightAfter"></slot>

            <div
                v-show="helpDocShow"
                @click="doHelp"
                class="help-pop markdown-body"
            >
                <div class="dialog" v-html="helpMD"></div>
            </div>
        </template>
    </div>
</template>
<script>
import { mapActions, mapGetters } from "vuex";
import md from "../../utils/md";
import help from "../../lang/help";
export default {
    data() {
        return {
            iconActive: {
                split: true,
                fullscreen: false
            },
            helpDocShow: false,
            helpMD: ""
        };
    },
    props: {
        dom: {
            default: () => {}
        },
        helpDoc: {
            type: String,
            default: ""
        },
        split: {
            type: Boolean,
            default: true
        },
        fullscreen: {
            type: Boolean,
            default: true
        },
        importBtn: {
            type: Boolean,
            default: true
        },
        exportBtn: {
            type: Boolean,
            default: true
        },
        printBtn: {
            type: Boolean,
            default: true
        },
        helpBtn: {
            type: Boolean,
            default: true
        },
        customRightToolbar: {
            type: Boolean,
            default: false
        }
    },
    computed: {
        ...mapGetters({ currentMD: "markdownBody/getTextareaContent" })
    },
    methods: {
        clickHandler(icon) {
            this.iconActive[icon] = !this.iconActive[icon];
            if (icon === "split") {
                this.setEditorSplitType(this.iconActive[icon]);
            } else if (icon === "fullscreen") {
                this.setEditorFullscreenType(this.iconActive[icon]);
            } else {
            }
        },
        ...mapActions({
            setEditorSplitType: "markdownBody/setEditorSplitType",
            setEditorFullscreenType: "markdownBody/setEditorFullscreenType",
            setTextareaContent: "markdownBody/setTextareaContent"
        }),
        addMDFromLocal(e) {
            const file = e.target.files[0];
            const reader = new FileReader();
            file && reader.readAsText(file);
            reader.onload = e => {
                const md = e.target.result;
                this.setTextareaContent(md);
            };
            e.target.value = "";
        },
        exportMd() {
            const md = this.currentMD;
            let downloadLink = document.createElement("a");
            downloadLink.setAttribute(
                "href",
                "data:text/plain;charset=UTF-8," + encodeURIComponent(md)
            );
            downloadLink.setAttribute(
                "download",
                this.$t("toolbar.right.downloadFileName")
            );
            downloadLink.style.display = "none";
            if (document.createEvent) {
                var event = document.createEvent("MouseEvents");
                event.initEvent("click", true, true);
                downloadLink.dispatchEvent(event);
            } else {
                downloadLink.click();
            }
        },
        doPrint() {
            // print render html
            const printDom = this.dom();
            const WinPrint = window.open(
                "",
                "",
                "left=0,top=0,width=800,height=900,toolbar=0,scrollbars=0,status=0"
            );
            WinPrint.document.write(printDom.innerHTML);
            WinPrint.document.close();
            WinPrint.focus();
            WinPrint.print();
            WinPrint.close();
        },
        doHelp() {
            this.helpDocShow = !this.helpDocShow;
        }
    },
    watch: {
        "$i18n.locale": {
            immediate: true,
            handler(val) {
                if (this.helpBtn && val) {
                    this.helpMD = md.render(help[val || "en"]);
                }
            }
        }
    }
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
        padding: 6px;
        &.hover {
            background: #e9e8e8;
        }
    }
    .selected {
        background: #e9e8e8;
    }
    .import {
        position: relative;
        input {
            position: absolute;
            width: 18px;
            left: 6px;
            top: 6px;
            opacity: 0;
        }
    }
    .help-pop {
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
            text-align: left;
            width: 50%;
            left: 25%;
            height: 60%;
            overflow-y: auto;
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
        }
    }
}
</style>
