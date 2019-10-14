<template>
    <div>
        <transition name="msgbox-fade">
            <div class="fm-cover-bg" :style="{'background': bg}" v-show="visible" @click="cancels" @touchmove="_stopDef"
                 @mousewheel="_stopDef"></div>
        </transition>
        <transition name="page-bottom-fade">
            <div class="fm-picker-box" :class="cla" v-show="visible">
                <div v-if="title" class="fm-picker-header" @touchmove="_stopDef" @mousewheel="_stopDef">
                    <div class="fm-picker-cancel" @click="cancels">{{cancel}}</div>
                    <!-- {{title}} -->
                    <div class="fm-picker-confirm" @click="submit">{{confirm}}</div>
                </div>
                <div class="fm-picker-content">
                    <cell v-if="days" :data.sync="days" :props-result.sync="ymd_d" @choose="_setDate"></cell>
                    <cell v-if="months" :data.sync="months" :props-result.sync="ymd_m" @choose="_setMonth"></cell>
                    <cell v-if="years" :data.sync="years" :props-result.sync="ymd_y" @choose="_setYear"></cell>
                </div>
                <hr class="fm-picker-top">
                <hr class="fm-picker-bottom">
            </div>
        </transition>
    </div>
</template>
<script>
    import cell from './cell.vue'

    export default {
        name: 'date-time',
        components: {cell},
        data: function () {
            return {
                result: [],
                days: null,
                ymd_y: {},
                ymd_m: {},
                ymd_d: {}
            }
        },
        mounted: function () {
            this.getDates()
            this.setValue()
        },
        watch: {
            result() {
                this.getDates()
            },
            value(val) {
                if(val && new Date(val)) {
                    this.setValue(this.parseTimeQueue(this.parseTime(val)))
                }
            }
        },
        methods: {
            setValue(ymd = this.myd_value) {
                setTimeout(() => {
                    this.ymd_y = ymd[0]
                    this.ymd_m = ymd[1]
                    this.ymd_d = ymd[2]
                }, 10)
            },
            cancels() {
                if (!this.title) {
                    this.submit()
                } else {
                    this.$emit('cancel')
                }
            },
            submit() {
                let myd = [...this.result], last_day = [...this.days].pop()
                myd[0] = this.result[0] ? this.result[0].toString(): this.ymd_y.name
                myd[1] = this.result[1] ? this.result[1].toString(): this.ymd_m.name
                myd[2] = this.result[2] ? (Number(this.result[2]) > Number(last_day.name) ? last_day.name : this.result[2].toString()): this.ymd_d.name       
                this.$emit('cancel', myd.toString().replace(',','-'))
                this.$emit('input', myd.toString().replace(/,/g,'-'))
            },
            _setYear(data) {
                let myd = [...this.result]
                myd[0] = data.data[data.index].name
                this.result = myd
            },
            _setMonth(data) {
                let myd = [...this.result]
                myd[1] = data.data[data.index].name
                this.result = myd
            },
            _setDate(data) {
                let myd = [...this.result]
                myd[2] = data.data[data.index].name
                this.result = myd
            },
            _stopDef(e) {
                e.preventDefault()
            },
            parseTime(t) {
                let _date = new Date(t), y = _date.getFullYear(), m = _date.getMonth() + 1, d = _date.getDate()
                return y + '-' + (m > 9 ? m : '0' + m) + '-' + (d > 9 ? d : '0' + d)
            },
            parseTimeQueue(t) {
                let n = this.parseTime(t).split('-')
                return [
                    {
                        id: n[0],
                        name: n[0].toString()
                    },
                    {
                        id: n[1],
                        name: n[1].toString()
                    },
                    {
                        id: n[2],
                        name: n[2].toString()
                    }
                ]
            },
            getDates() {
                let vm = this,
                    y = this.result[0] ? vm.result[0] : vm.ymd_y.name,
                    m = this.result[1] ? parseInt(vm.result[1]) : parseInt(vm.ymd_m.name),
                    s = new Date().setFullYear(y, m - 1, 1),
                    e = new Date().setFullYear(y, m, 1),
                    n = (e - s)/ 86400000,
                    d = [...this.min_date]
                for (let i = 29; i <= n; i ++) {
                    d.push({
                        id: i,
                        name: i
                    })
                }
                this.days = d
            },
            getNowTime() {
                let vm = this
                if (this.value && typeof this.value === 'object') {
                    return this.value
                } else {
                    if (new Date(vm.start).getTime() >= new Date().setHours(0, 0, 0, 0)) {
                        return this.start
                    }
                    if (new Date(vm.end).getTime() <= new Date().setHours(0, 0, 0, 0)) {
                        return this.end
                    }
                    return this.parseTime(new Date().setHours(0, 0, 0, 0))
                }
            }
        },
        computed: {
            min_date() {
                let d = []
                for (let i = 1; i <= 28; i++) {
                    d.push({
                        id: i,
                        name: i > 9 ? i.toString() : '0' + i
                    })
                }
                return d
            },
            years() {
                let y = [], vm = this, s = new Date(vm.start).getFullYear(), e = new Date(vm.end).getFullYear()
                for (let i = s; i <= e; i++) {
                    y.push({
                        id: i,
                        name: i.toString()
                    })
                }
                return y
            },
            months() {
                let m = []
                for (let i = 1; i <= 12; i++) {
                    m.push({
                        id: i,
                        name: i > 9 ? i.toString() : '0' + i
                    })
                }
                return m
            },
            myd_value() {
                return this.parseTimeQueue(this.getNowTime())
            }
        },
        props: {
            'value': {
                type: String,
                default: null
            },
            'start': {
                type: String,
                default: '1900-01-01'
            },
            'end': {
                type: String,
                default: '2100-12-30'
            },
            'visible': {
                type: Boolean,
                default: false
            },
            'title': {
                type: String,
                default: ''
            },
            'cla': {
                type: String,
                default: ''
            },
            'bg': {
                type: String,
                default: 'rgba(0,0,0,.35)'
            },
            'confirm': {
                type: String,
                default: '确定'
            },
            'cancel': {
                type: String,
                default: '取消'
            }
        }
    }
</script>
<style lang="">
@keyframes loading-dash {
    0% {
        stroke-dasharray: 1, 200;
        stroke-dashoffset: 0;
    }
    50% {
        stroke-dasharray: 90, 150;
        stroke-dashoffset: -40px;
    }
    100% {
        stroke-dasharray: 90, 150;
        stroke-dashoffset: -120px;
    }
}
@-webkit-keyframes loading-dash {
    0% {
        stroke-dasharray: 1, 200;
        stroke-dashoffset: 0;
    }
    50% {
        stroke-dasharray: 90, 150;
        stroke-dashoffset: -40px;
    }
    100% {
        stroke-dasharray: 90, 150;
        stroke-dashoffset: -120px;
    }
}
.fm-load-fade-enter-active, .fm-load-fade-leave-active {
    transition: opacity .5s;
}
.fm-load-fade-enter, .fm-load-fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
    opacity: 0;
}


.msgbox-fade-enter-active {
    -webkit-animation: msgbox-fade-in .3s;
    animation: msgbox-fade-in .3s
}

.msgbox-fade-leave-active {
    -webkit-animation: msgbox-fade-out .3s;
    animation: msgbox-fade-out .3s
}

@-webkit-keyframes msgbox-fade-in {
    0% {
        opacity: 0
    }
    100% {
        opacity: 1
    }
}

@keyframes msgbox-fade-in {
    0% {
        opacity: 0
    }
    100% {
        opacity: 1
    }
}

@-webkit-keyframes msgbox-fade-out {
    0% {
        opacity: 1
    }
    100% {
        opacity: 0
    }
}

@keyframes msgbox-fade-out {
    0% {
        opacity: 1
    }
    100% {
        opacity: 0
    }
}


.page-top-fade-enter-active {
    -webkit-animation: page-top-fade-in .3s;
    animation: page-top-fade-in .3s
}

.page-top-fade-leave-active {
    -webkit-animation: page-top-fade-out .3s;
    animation: page-top-fade-out .3s
}

@-webkit-keyframes page-top-fade-in {
    0% {
        -webkit-transform: translate3d(0, -100%, 0);
        transform: translate3d(0, -100%, 0);
        opacity: 0
    }
    100% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
}

@keyframes page-top-fade-in {
    0% {
        -webkit-transform: translate3d(0, -100%, 0);
        transform: translate3d(0, -100%, 0);
        opacity: 0
    }
    100% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
}

@-webkit-keyframes page-top-fade-out {
    0% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
    100% {
        -webkit-transform: translate3d(0, -100%, 0);
        transform: translate3d(0, -100%, 0);
        opacity: 0
    }
}

@keyframes page-top-fade-out {
    0% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
    100% {
        -webkit-transform: translate3d(0, -100%, 0);
        transform: translate3d(0, -100%, 0);
        opacity: 0
    }
}

.page-bottom-fade-enter-active {
    -webkit-animation: page-bottom-fade-in .3s;
    animation: page-bottom-fade-in .3s
}

.page-bottom-fade-leave-active {
    -webkit-animation: page-bottom-fade-out .3s;
    animation: page-bottom-fade-out .3s
}

@-webkit-keyframes page-bottom-fade-in {
    0% {
        -webkit-transform: translate3d(0, 100%, 0);
        transform: translate3d(0, 100%, 0);
        opacity: 0
    }
    100% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
}

@keyframes page-bottom-fade-in {
    0% {
        -webkit-transform: translate3d(0, 100%, 0);
        transform: translate3d(0, 100%, 0);
        opacity: 0
    }
    100% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
}

@-webkit-keyframes page-bottom-fade-out {
    0% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
    100% {
        -webkit-transform: translate3d(0, 100%, 0);
        transform: translate3d(0, 100%, 0);
        opacity: 0
    }
}

@keyframes page-bottom-fade-out {
    0% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
    100% {
        -webkit-transform: translate3d(0, 100%, 0);
        transform: translate3d(0, 100%, 0);
        opacity: 0
    }
}

.page-left-fade-enter-active {
    -webkit-animation: page-left-fade-in .3s;
    animation: page-left-fade-in .3s
}

.page-left-fade-leave-active {
    -webkit-animation: page-left-fade-out .3s;
    animation: page-left-fade-out .3s
}

@-webkit-keyframes page-left-fade-in {
    0% {
        -webkit-transform: translate3d(-100%, 0, 0);
        transform: translate3d(-100%, 0, 0);
        opacity: 0
    }
    100% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
}

@keyframes page-left-fade-in {
    0% {
        -webkit-transform: translate3d(-100%, 0, 0);
        transform: translate3d(-100%, 0, 0);
        opacity: 0
    }
    100% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
}

@-webkit-keyframes page-left-fade-out {
    0% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
    100% {
        -webkit-transform: translate3d(-100%, 0, 0);
        transform: translate3d(-100%, 0, 0);
        opacity: 0
    }
}

@keyframes page-left-fade-out {
    0% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
    100% {
        -webkit-transform: translate3d(-100%, 0, 0);
        transform: translate3d(-100%, 0, 0);
        opacity: 0
    }
}

.page-right-fade-enter-active {
    -webkit-animation: page-right-fade-in .3s;
    animation: page-right-fade-in .3s
}

.page-right-fade-leave-active {
    -webkit-animation: page-right-fade-out .3s;
    animation: page-right-fade-out .3s
}

@-webkit-keyframes page-right-fade-in {
    0% {
        -webkit-transform: translate3d(100%, 0, 0);
        transform: translate3d(100%, 0, 0);
        opacity: 0
    }
    100% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
}

@keyframes page-right-fade-in {
    0% {
        -webkit-transform: translate3d(100%, 0, 0);
        transform: translate3d(100%, 0, 0);
        opacity: 0
    }
    100% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
}

@-webkit-keyframes page-right-fade-out {
    0% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
    100% {
        -webkit-transform: translate3d(100%, 0, 0);
        transform: translate3d(100%, 0, 0);
        opacity: 0
    }
}

@keyframes page-right-fade-out {
    0% {
        -webkit-transform: translate3d(0, 0, 0);
        transform: translate3d(0, 0, 0);
        opacity: 1
    }
    100% {
        -webkit-transform: translate3d(100%, 0, 0);
        transform: translate3d(100%, 0, 0);
        opacity: 0
    }
}

</style>
