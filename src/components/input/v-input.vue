<template>
    <div
        class="form-el-content form-input"
        :class="{ 'error-group': dataKey.error, [dataKey.css]: true }"
        v-show="dataKey.show"
    >
        <input
            :type="dataKey.type"
            :maxlength="dataKey.maxlength"
            :placeholder="dataKey.placeholder"
            :disabled="dataKey.disabled"
            class="text"
            :name="dataKey.name"
            :class="{ 'text-password': dataKey.hasEye }"
            @input="changeValue()"
            @focus="focused = true"
            @blur="blurEvt()"
            v-model="dataKey.val"
            v-manualevent="evtHandlerList"
            :evt-name="evtName"
            ref="input"
        />
        <div
            @click="focus()"
            class="placeholder-text"
            v-if="!supportPlaceholder && !dataKey.val"
        >
            {{ dataKey.placeholder }}
        </div>
        <div
            v-if="dataKey.hasEye"
            :class="
                dataKey.type == 'password'
                    ? 'v-icon-eye-close'
                    : 'v-icon-eye-open'
            "
            @click="changePlaceHolder()"
        ></div>
        <div class="error-bottom text-error" v-if="dataKey.error">
            {{ dataKey.error }}
        </div>
    </div>
</template>

<script>
import addEvent from "../add-event";

let defaults = {
    required: true,     //是否必须输入
    css: "",            //样式
    show: true,         //是否显示
    ignore: false,      //是否忽略
    disabled: false,    //是否禁用
    maxlength: "",      //最大输入长度
    type: "text",       //输入框类型
    placeholder: "",    //占位提示文字
    hasEye: "",         //是否有小眼睛
    name: "",           //名称字段
    val: "",            //组件value
    error: "",          //错误标志
    isNum: false,       //是否输入框内允许只输入数字或浮点型
    valid: [],          //数据验证
    changeCallBack: function() {}
};
export default {
    name: "v-input",
    props: ["data-key"],
    mixins: [addEvent],
    created() {
        this.dataKey = this.setOptions(this.dataKey, defaults);
        this.addEvent();
    },
    data() {
        return {
            //是否支持placehodler
            supportPlaceholder: this.hasPlaceholder(),
            // 记录当前是否是活动状态
            focused: false
        };
    },

    methods: {
        changePlaceHolder() {
            if (this.dataKey.type == "password") {
                this.dataKey.type = "text";
            } else {
                this.dataKey.type = "password";
            }
        },

        changeValue() {
            let isCheckTrue = this.check(this.dataKey);
            if (!isCheckTrue) {
                return;
            }

            this.dataKey.changeCallBack(this.dataKey.val);
        },
        hasPlaceholder() {
            var i = document.createElement("input");
            return "placeholder" in i;
        },
        check(dataObj) {
            if (typeof this.$checkData == "function") {
                return this.$checkData(dataObj);
            }

            return true;
        },
        blurEvt() {
            this.focused = false;
            if (this.dataKey.isNum && this.dataKey.val) {
                //输入框只允许输入数字
                this.dataKey.val = Number(this.dataKey.val).toString();
            }
        },
        focus() {
            this.$refs.input.focus();
        }
    },
    watch: {
        "dataKey.show": {
            handler(newValue, oldValue) {
                if (!newValue) {
                    this.dataKey.error = "";
                }
            }
        },
        "dataKey.disabled": {
            handler(newValue, oldValue) {
                if (!newValue) {
                    this.dataKey.error = "";
                }
            }
        }
    },
    destroyed() {
        this.dataKey.error = "";
    }
};
</script>
